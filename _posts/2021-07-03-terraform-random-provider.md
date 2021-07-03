---
title: "Terraform: Random 简介"
layout: post
guid: USe8xm7ltUKLzAF60yP9
date: 2021-07-03 14:41:39
coffee:
tags:
  -
---

## Provider 简介

Provider 是连接 Terraform 和云的桥梁，Terraform 可以通过不同的插件与不同的云打交道，甚至私有云。

Provider 定义了各种资源，比如 EC2，RDS，VPC，NAT 等等。

比如你可以通过 [terraform AWS provider](https://github.com/hashicorp/terraform-provider-aws) 创建AWS 对应的各种资源。

你也可以使用 [Google Cloud Platform provider](https://github.com/hashicorp/terraform-provider-google) 创建各种资源。

![](https://mednoter.com/media/files/2021/2021-07-03_16-16-44.jpg)


## Terraform Random 简介

[Terraform Random](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/uuid) 它是一个"逻辑层"的 provider。之所以称之为”逻辑provider“，是因为它背后并没有对应的云。

它提供了以下资源类型。

- random_id，用来创建随机字符串，比如 `9baf56f751636fcf`。
- random_integer，用来创建随机数字。
- random_pet, 用来创建一个随机动物名，比如 `dog`, `cat`, `panda`。
- random_string，可以用创建含有特殊字符的字符串，比如 `/@£$4iLAWXjfUPHE`，可以用来做密码。
- random_uuid，创建一个 uuid，比如 `a2af1b5b-fdf2-04ce-b85e-cd235d08b76e`。


下面是一段示例代码，它首先创建了一个 VPC，然后创建了一台服务器，服务器的名字引用了 random_pet 的值。

```terraform
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 3.4.0"
    }

    random = {
      source = "hashicorp/random"
      version = "2.3.0"
    }
  }
}

provider "aws" {
  region = "us-west-2"
}


module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  name            = "test_vpc" 
  cidr            = "10.0.0.0/16"
  azs             = ["us-west-2a"]
  public_subnets  = ["10.0.1.0/24", "10.0.2.0/24"]
  private_subnets = ["10.0.101.0/24", "10.0.102.0/24"]
  enable_nat_gateway = true
}

resource "random_pet" "server" {
}

resource "aws_instance" "server" {
  ami = "ami-a0cfeed8"
  instance_type = "t3.nano"
  tags = {
    Name = "web-server-${random_pet.server.id}"
  }
}

```

运行完代码之后，可以在 AWS Console 看到一台名字叫 “惊讶的花栗鼠”的服务器。

![](https://mednoter.com/media/files/2021/2021-07-03_15-32-23.png)

如果你要运行以上代码，请确保你拥有一个AWS 账户，并且你的电脑上配置好 [AWS Command Line](https://aws.amazon.com/cli/)。

## Terraform Random 是随机函数吗？

过去所积累的知识可以帮我们快速学习新知识，比如掌握 Ruby 语言，再去学 Python 会更快。掌握了英语，学西班牙语也更快。

然而在这个 Terraform Random 知识点上，却适得其反。编程语言中的 Random 函数反而让我无法理解 Terraform Random。

**我的疑惑：如果我的资源包含 Random 的属性，这个属性的值会变来变去吗？**

比如第一次用 Terraform 创建服务器，它名字是“惊讶的花栗鼠”，第二次运行，它的名字变为 “威严的老虎”，第三次运行，它变为 ”澳洲考拉“。

抑或我运行 Terraform 创建数据库，它的密码是 "welldone,Ryan!"，第二次运行后，密码被更新为 "YouAreAwesome,Ryan!"，第三次运行后，密码又被更新。

经过多次实验我发现，重复运行 Terraform后，服务器的名字并没有变来变去。

原来 Terraform 把 random 产生的值保存到了 `terrraform.tfstate` 中。

```
// terraform.tfstate
// 省略无关代码
...
    {
      "mode": "managed",
      "type": "random_pet",
      "name": "server",
      "provider": "provider[\"registry.terraform.io/hashicorp/random\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "id": "amazed-chipmunk",
            "keepers": null,
            "length": 2,
            "prefix": null,
            "separator": "-"
          },
          "sensitive_attributes": [],
          "private": "bnVsbA=="
        }
      ]
    }
...
// 省略无关代码
```

这样看来，我们应该把 Terraform Random “一个资源”，而不是一个函数。

- 它用 random 函数创建了一个值。
- 然后把值保存到 terraform state 中。
- 这个资源的值可以被其他资源引用
- 每次运行时读取文件中的值。
