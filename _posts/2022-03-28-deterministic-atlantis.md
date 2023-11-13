---
title: "Atlantis: 可确定的执行计划"
layout: post
guid: i1YEusCcckZsK
date: 2022-03-28 21:07:40
tags:
  -
---

![](https://mednoter.com/media/files/2022/2022-03-28-atlantis-workflow.png)

Workstream 的基础架构完全用 Terraform 来编写，就是大家常说的架构即代码 Infrastructure as Code (IaC)，并且用自动化工具 [Atlantis](https://www.runatlantis.io/) 来执行。每次有工程师要修改架构，只需要修改 Terraform 代码，然后在 Github 提交一个Pull Request。如果 PR 通过审核，Atlantis 可以帮助执行 terraform apply，并且合并 Pull Request。

在使用 Atlantis 的过程中我发现它不稳定。

1. Atlantis 经常打印出一堆和代码改动无关的执行计划。比如，我只是给一些资源加了一个标签`team=Kraken`, Atlantis 在做计划时除了增加 tag，它还要给 Security Group 增加一个 Egress 的规则。这就令代码审核人员困惑。
2. 一个月前的 Terraform 代码，没有人碰过，再次运行会失败。

Atlantis 执行老代码，变成了一种玄学。代码越老，出问题的概率越大。


## 为什么会出现这种情况呢？

有些人可能说"你没有使用 `.terraform.lock.hcl` 锁定依赖，所以不同的人执行时，会使用不同版本的 module， 产生不同的结果。"

其实加了锁文件也没用。Terraform 的锁文件功能非常薄弱，只能锁定 provider 的版本，并不能锁定 module 版本。当 Atlantis 执行 `terraform apply` 时，Terraform 会下载和使用 module 的最新版本，这就导致了预期外的架构变动。

> [Terraform Documentation](https://www.terraform.io/language/files/dependency-lock#dependency-lock-file):
>
> At present, the dependency lock file tracks only provider dependencies. Terraform does not remember version selections for remote modules, and so Terraform will always select the newest available module version that meets the specified version constraints. You can use an exact version constraint to ensure that Terraform will always select the same module version.

## 如何避免？

如果我们能锁定 provider 和 module 的版本，Atlantis 执行 terraform plan 或 terraform apply 时，就会自始至终使用你所选取的版本，结果永远是恒定的（Deterministic）。

**首先，在代码中显式的指定 module 的版本，锁死 module 的版本。**

```
module "webserver_sg" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "4.9.0"  # 👈 看这里

  name        = "${var.project}-webserver"
  vpc_id      = module.vpc.vpc_id

  ingress_with_source_security_group_id = [
    {
      rule = "http-80-tcp"
      source_security_group_id = module.alb_sg.security_group_id
    }
  ]
  tags = local.tags
}
```

**其次，锁定 provider 的版本。**

最简单的办法是把 .terraform.lock.hcl 添加到 git 中，锁死 provider 的版本。

这样 Atlantis 在执行代码时，就会稳定一些，避免了各种玄学问题。