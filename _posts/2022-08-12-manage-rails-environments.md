---
title: "Rails 应用的环境管理经验"
layout: post
guid: XKPa1KmnJCcD
date: 2022-08-12
coffee:
tags:
  -
---

当使用命令行 `rails new` 去创建一个新项目时，Rails 默认会创建三个环境：

- development: 本地开发环境。
- test: 用来跑测试。
- production: 生产环境，用来服务客户。

在项目的最早期，还处于验证想法阶段。工程师的工作流程也比较粗狂，写代码，跑测试，然后直接部署生产环境。

**少量客户阶段**

产品不断迭代，逐渐的有客户开始使用产品。为了保证工程质量，这时候需要给 Rails 添加一个预发布环境，通常大家都命名为 staging 环境，工作流程也开始严谨：

1. 工程师在本地开发新功能。（developer 环境）
2. 在CI（或本地）跑测试。（test 环境）
3. 代码部署到 staging 环境，QA 工程师测试新功能。
4. 如果没有bug，则部署到 production 环境，供客户使用。

```
environments
├── development.rb
├── staging.rb
├── production.rb
└── test.rb
```

**大量客户 + 多团队协作 + 多 QA**

如果产品市场反响良好，客户越来越多。工程师团队规模也由几个人扩增到几十人或上百人。人员众多，且共用一个 staging 环境，合并代码到 staging 分支时容易冲突。

有些程序员比较耐心，遇到代码冲突会联系原作者，一起解决。

如果是跨国团队，因为时差的原因，一来一回沟通需要一天时间，非常要命。这时候程序员可能就没了耐心，直接重置 staging 公共分支，这会导致别人正在测试的功能凭空消失，QA 工程师上报 bug，结果细查下来是代码重置导致的。

为了降低代码冲突和沟通成本，很多公司会选择创建更多的预发布(staging)环境：

- staging 给 QA 专用，用于上线前的回归测试。
- staging-1 给 A 团队使用
- staging-2 给 B 团队使用
- staging-3 给 C 团队使用
- staging-4 给 D 团队使用
- staging-5 给 E 团队使用
- ...
- staging-x 给 x 团队使用

但工程师越多，需要的 Rails 环境也越多，创建和维护环境变成 DevOps 团队的体力活。

如何创建新环境呢？

## 第一种方案： 在 Rails 的 `config/environments` 目录下创建6个环境的配置文件。

这是 Rails 推荐的配置风格，原汁原味。

![](/media/files/2022/2022-08-12-08-41-03-stagings.jpg)

但事情没那么简单，除了 `config/environments`，还有许多额外工作。我们要在 `config/database.yml` 添加6个新环境的数据库配置。

![](/media/files/2022/2022-08-12-database-yml.jpg)

此外，有些工程师喜欢在常量定义配置，那我们还需要检查各种常量，确保新创建的环境，都有赋值。尤其是在犄角旮旯里定义的变量，如果没有察觉，新环境会一直报错。所以需要全局搜索 `Rails.env`，免得遗漏。

```
# config/initializers/sidekiq.rb

case Rails.env
when :staging-1
  REDIS_HOST = 'redis-s1.3922002.redis.aws.com:6379'
when :staging-2
  REDIS_HOST = 'redis-s2.3922002.redis.aws.com:6379'
...
...
```

在常量里定义配置，糟糕透顶，有品位的工程师会把不同环境的配置抽象出来，放到不同的文件中，并且用一些库来管理配置，Rails 常用的库是 [rubyconfig/config](https://github.com/rubyconfig/config)。如果团队在使用这些库，我们也需要为新环境添加配置文件。

```
config/settings.yml
config/settings.local.yml
config/settings/development.yml
config/settings/production.yml
config/settings/test.yml
config/settings/staging.yml
config/settings/staging-1.yml
config/settings/staging-2.yml
config/settings/staging-3.yml
config/settings/staging-4.yml
config/settings/staging-5.yml
```


总之，以上都不是万全之策，有诸多缺点：

1. 即使有完备的内部文档，搭建新环境也非常耗时耗力，全套走下来，工程量不小。
2. 修改常量很危险，创建环境会触碰老代码，稍有不慎就会毁掉所有环境，违背了开放封闭原则（Open–closed principle）。
3. 安全性差。配置中包含 client_secret，token，私钥，如果代码不幸泄露，各种秘钥被一锅端。针对这个问题，Rails 6 推出了 [Rails Credential](https://edgeguides.rubyonrails.org/security.html#environmental-security) 这个功能。
3. docker 镜像混杂了各种配置信息，不再干净。

正因为如此多的缺点，大家不约而同的把配置信息放到环境变量中，并且归纳总结了一些最佳实践供后人学习。

## The 12-factor Application

Heroku 写过一篇 SaaS 架构文章 《[12-factor  Application](https://12factor.net/)》，里面描述了设计 SaaS 应用的12条原则，被奉为圭臬。时至今日，依然无出其右。

### 第一条原则：一份基准代码（Codebase），多份部署（deploy）

> 尽管每个应用只对应一份基准代码，但可以同时存在多份部署(deploy)。每份部署相当于运行了一个应用的实例。通常会有一个生产环境，一个或多个预发布环境。

![](/media/files/2022/2022-08-12-codebase-deploys.png)

### 第二条原则：显式声明依赖关系（ dependency ）

同样的代码，在不同机器，依赖要一致，行为也要一致，比如：

- JavaScript 使用 npm 或 yarn 来管理各种库的依赖。
- Ruby 使用 Bundler 来管理各种库的版本依赖。
- Docker 不但打包代码的库的依赖，还打包了操作系统的依赖。任何人获得 docker image 后都可以运行代码，不会出现这种尴尬情况：一份代码只能在我的电脑上跑，不能在你的电脑上跑。

### 第三条原则：在环境中存储配置

> [The 12-factor App: 配置](https://12factor.net/zh_cn/config)
> 
> 12-Factor 推荐将应用的配置存储于环境变量中（ env vars, env ）。环境变量可以非常方便地在不同的部署间做修改，却不动一行代码。

**Docker**

比如 Docker 允许你把配置放到环境变量中，从而创建不同的实例（container）。

```
docker run --name postgresql \
  -e POSTGRES_USER=myusername \
  -e POSTGRES_PASSWORD=mypassword \
  -p 5432:5432 -v /data:/var/lib/postgresql/data \
  -d postgres
```

**Helm**

[Helm](https://helm.sh/) 是一个 Kubernetes 应用的包管理工具，它有三个核心概念，chart，config，release。

- chart 是模版
- config 是配置信息

chart + config = release （一个部署的实例）


前文所述的第一种方案，每次添加新环境都要改动代码，完全不符合 12-factor Application 的原则，所以还需要继续优化，这就引出了第二种方案。

## 第二种方案：代码无状态，配置信息与代码分离。

首先，保证代码中所有的配置信息都取自环境变量，代码无状态。

比如，DB 的配置，要取自环境变量。

```yml
# config/database.yml
# ...
production:
  <<: *default
  database: <%= ENV['DATABASE_NAME' %>
  host: <%= ENV['DATABASE_HOST'] %>
  password: <%= ENV['DATABASE_PASSWORD'] %>
```

Sidekiq 的配置也取自环境变量。

```ruby
Sidekiq.configure_server do |config|
  config.redis = {
    host: ENV['REDIS_HOST'],
    port: 6379
  }
end
# ...
```

任何逻辑，但凡和环境相关，其配置都取自环境变量。

```ruby
class OauthController < ApiController
  def redirect
    redirect_to(ENV['GOOGLE_OAUTH_URL'])
  end
end
```

第二步，我们不再称呼 staging-1, staging-2, staging-3, staging-4, staging-5, ... staging-x，production 为环境，而是把他们看做代码的运行实例，称之为部署 (deploy)，每个部署都有自己的配置文件。

如果你使用的是 AWS，可以把某个部署的配置保存在 [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)，敏感信息可以保存在 [AWS secret manager](https://aws.amazon.com/secrets-manager/)。

```
# 创建 staging-1 的配置信息
aws ssm put-parameter \
    --name "staging-1-configuration" \
    --value "parameter-value" \
    --type String \
    --tags "DB_HOST=xxx,DB_USER=xxx,DB_PASSWORD=xxx,REDIS_HOST=xxx,GOOGLE_OAUTH_URL=xxxx"
```

如果你是用的是 Kubernetes，可以把不同部署的配置信息保存在不同的 [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/)，敏感信息保存在 [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) 中。

```
kubectl create configmap staging1-config-map \
  --from-literral="DB_HOST=xxx" \
  --from-literral="DB_USER=postgres" \
  --from-literral="DB_PASSWORD=xxx"
```


第三步，我们把 `config/environments` 下的三个文件 production.rb / staging.rb /development.rb / test.rb 看做 Rails 不同的运行模式（mode），而不是看做运行实例。

换言之，无论是 production, staging-1， staging-2， staging-3， staging-4，staging-5，还是将来的 staging-x，它们可以选择 `production` 运行模式，亦或选择 `staging` 运行模式，每个人可以根据自己的实际情况来决定。

在本文中，所有的部署都使用 production mode 去运行。

```
export .env.staging-1
RAILS_ENV=production rails s
```

![](/media/files/2022/2022-08-12_14-07-10-production-yml.jpg)

第四步： 代码 + 不同的配置 = 不同的部署

如果你使用的是 Capistrano 部署代码，那么

```
Code + 不同配置 = 不同的部署
```

如果你使用的是 Docker 部署，那么：

```
Docker Image + 不同配置 = 不同的部署
```

如果你使用的是 Kubenetes，将不同配置文件注入到了Pod 中，就变成了不同的部署 (deploy)。

```yaml
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    name: webapp
  name: webapp
  namespace: default
spec:
  containers:
  - name: web-app
    image: web-app:staging-1
    envFrom:
    - configMapRef:  👈 看这里
        name: staging-1-config-map
    - secretRef:     👈 看这里
        name: staging-1-secrets
```


### 优点

创建部署，只需要准备一份新的配置文件就可以，省事省力。

这种方案也保证了安全。代码中不包含机密信息，即使代码泄露也不会扩大风险。不同部署的配置文件可以设置不同的访问权限，比如仅仅允许 DevOps 团队访问 production deploy 的配置信息。

在本文中所有部署的运行模式都是 production mode，以此消除了不同部署的差异 ([parity](https://12factor.net/dev-prod-parity))。


### 第二种方案的缺点

NewRelic, Datadog, Sentry 等监控工具默认会把 `Rails.env`附着在日志(log)，异常（error)，性能数据上，方便过滤。

![](/media/files/2022/2022-08-12_12-47-48.jpg)

在第二种方案中，所有的部署的运行模式都为 production mode，这导致所有监控数据都混在 production mode 下。遇到问题，工程师根本无法区分是哪个部署出了问题。

![](/media/files/2022/2022-08-12_12-56-12-only-production.jpg)

## 如何解决监控工具的问题？

其实 NewRelic, Datadog, Sentry 提供了接口，允许我们自定义实例的名字，以 Sentry 举例，它的语法如下：
 
 ```ruby
Sentry.init do |config|
  #...
  config.environment = "你喜欢的部署名"
end
```

因此我们可以在 staging-1, staging-2, staging-x, production 等不同部署中的配置中，引入一个新的变量 "DEPLOYMENT_ID" 来声明部署的名称。初始化各种监控工具时，读取 ENV["DEPLOY_ID"] 的值。
 
假如 staging-1 的配置信息如下：

```
DEPLOYMENT_ID=staging-1
DB_HOST=staging-1.db
DB_USER=postgres
...
```

我们可以这样配置 Sentry：

```ruby
Sentry.init do |config|
  #...
  config.environment = ENV['DEPLOYMENT_ID'] # 它的值为 staging-1
end
```

可以这样配置 Datadog：

```ruby
Datadog.configure do |c|
  # ...
  c.env = ENV['DEPLOYMENT_ID'] # 它的值为 staging-1
end
```

这样就能保证监控工具的正常显示了。

![](/media/files/2022/2022-08-12_20-35-02-deploy.md)


## 迷惑的概念

你在读完文章之后或许对 Deploy 和 Environment 这两个概念更加疑惑。这种的困惑来自于固有印象：每个应用仅有一个 production 部署。

现实世界并非如此。

**很多 SaaS 应用支持大客户的私有化部署**。同样一套代码，会卖给中国电信，中石油，公安系统。代码部署在他们各自的私有云上，运行模式都为 production mode。

**不同国家也有不同的数据治理策略**。中国要求 Apple iCloud 的数据必须存在云上贵州。iCloud 同一套代码部署在中国和美国，其运行模式都为 production mode。抖音在美国部署在 Oracle Cloud上，在中国则部署在自己的机房里，他们的运行模式也都为 production mode。

12-factor 提出的部署（deploy）这个概念是比环境（Environment）更加精确的概念。

总结一下：

- 把 production / staging 看做运行模式。
- 把 staging-1, staging-2, staging-3, staging-4, staging-5, production 看做代码的运行的实例。

![](/media/files/2022/2022-08-12-codebase-build-config-deploy.png)


**Note**

本文中的方案，来自于 Workstream 同事们的实践经验，我只是提笔记录下来，并非我的工作成果。

特别感谢 Louise Xu, Felix Chen, Vincent Huang, Teddy Wang 的审校和反馈。

