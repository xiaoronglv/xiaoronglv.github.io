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

产品不断迭代，逐渐的有客户开始使用产品。为了保证工程质量，这时候需要给 Rails 添加一个非生产环境，通常大家都命名为 "staging"环境，工作流程也开始严谨：

1. 工程师在本地开发新功能。（developer 环境）
2. 在CI（或本地）跑测试。（test 环境）
3. 代码部署到 staging 环境，QA 工程师测试新功能 （staging环境）
4. 如果没有bug，则部署到 production 环境，供客户使用。

```
environments
├── development.rb
├── staging.rb
├── production.rb
└── test.rb
```

**大量客户 + 多团队协作 + 多 QA**

如果产品有市场，客户越来越多。团队规模也由几个人扩增到几十人或上百人。人员众多，且共用一个 staging 环境，合并代码发布到 staging 时就会有越来越多的代码冲突。

有些程序员比较耐心，遇到冲突会联系原作者，解决代码冲突。

但是如果是跨国团队，一来一回需要一天时间，非常要命。这时候程序员可能就没有耐心，选择直接重置 staging 的 git 分支。这会导致一系列的问题，比如别人正在测试的功能就消失了，QA 工程师认为是 bug，上报，结果细查下来是代码重置导致的。

为了降低冲突和沟通成本，很多公司会选择创建更多的 staging 环境：

- staging 给 QA 专用，用于上线前的回归测试。
- staging-1 给 A 团队使用
- staging-2 给 B 团队使用
- staging-3 给 C 团队使用
- staging-4 给 D 团队使用
- staging-5 给 E 团队使用

工程师越多，需要创建的 Rails 环境也越多，环境的管理和创建是个头疼的问题。

## 第一种方案： 在 Rails 的 `config/environments` 目录下创建6个环境的配置文件。

这种方法简单，且遵循了原汁原味的 Rails 风格。

![](/media/files/2022/2022-08-12-08-41-03-stagings.jpg)

但除了 `config/environments`，还有许多需要额外的工作。

第二步，在 database.yml 添加6个环境的数据库配置。

![](/media/files/2022/2022-08-12-database-yml.jpg)

第三步，有些工程师喜欢把一些配置放到常量里。我们需要检查各种常量，确保新创建的环境，都有对应的值。

```

case Rails.env
when :staging-1
  REDIS_HOST = 'redis-s1.3922002.redis.aws.com:6379'
when :staging-2
  REDIS_HOST = 'redis-s2.3922002.redis.aws.com:6379'
...
...
```

第四步，有些工程师喜欢用一些库来管理配置，比较常用的是 [rubyconfig/config](https://github.com/rubyconfig/config)。我们需要为新创建的环境添加配置文件。

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

第五步，全局搜索 `Rails.env` ，逐一检查，免得遗漏。

全套走下来，工程量不小，也带来诸多缺点：

1. 如果在犄角旮旯里配置了和环境相关的变量，而你没有察觉，新环境会一直报错。
2. 修改常量很危险。每次增加环境会改动老代码，稍有不慎就会把所有环境毁了。违背了开放封闭原则（Open–closed principle）。
3. 安全性差。配置中可能包含 client_secret，token，私钥。如果代码不幸泄露，各种秘钥也被一锅端。针对这个问题，Rails 6 推出了 [Rails Credential](https://edgeguides.rubyonrails.org/security.html#environmental-security) 这个功能。
3. docker 镜像混杂了各种配置信息，不再干净。

我想正因为这种方案的诸多缺点，大家在实践时，才会不约而同选择把配置信息放到环境变量中，这不得不提一下 12-factors。


## 12-factors

Heroku 写过一篇奉为圭臬的 SaaS 架构文章 《[12-factors application](https://12factor.net/)》，里面描述了设计 SaaS 应用时12条原则。时至今日，其地位依然无法撼动。

### 第一条原则：一份基准代码（Codebase），多份部署（deploy）

> 尽管每个应用只对应一份基准代码，但可以同时存在多份部署(deploy)。每份部署相当于运行了一个应用的实例。通常会有一个生产环境，一个或多个预发布环境。

![](/media/files/2022/2022-08-12-codebase-deploys.png)

### 第二条原则：显式声明依赖关系（ dependency ）

同样的代码，在不同机器，依赖要一致，行为也要一致。人们为此付出了诸多努力，比如：

- JavaScript 使用 npm 或 yarn 来管理各种库的依赖。
- Ruby 使用 Bundler 来管理各种库的版本依赖。
- Docker 不但打包代码的库的依赖，还打包了操作系统的依赖。任何人获得 docker image 后都可以运行代码，不会出现这种尴尬情况：一份代码只能在我的电脑上跑，不能在你的电脑上跑。

### 第三条原则：在环境中存储配置

> [the 12-factor app: config](https://12factor.net/zh_cn/config)
> 
> 12-Factor推荐将应用的配置存储于环境变量中（ env vars, env ）。环境变量可以非常方便地在不同的部署间做修改，却不动一行代码。

**Docker**

比如 Docker 允许你把配置放到环境变量中，从而创建不同的 container 实例。

```
docker run --name postgresql \
  -e POSTGRES_USER=myusername \
  -e POSTGRES_PASSWORD=mypassword \
  -p 5432:5432 -v /data:/var/lib/postgresql/data \
  -d postgres
```

**Helm**

[Helm](https://helm.sh/) 是一个 Kubernetes 应用的包管理工具，它有三个核心概念，chart，config，release。它也与 12-factor 相通。

- chart 是模版
- config 是配置信息

chart + config = release （一个部署的实例）

**由此可见，我们的第一种实现方案，每次添加新环境都要改动代码，完全不符合 12-factor application 的原则。有没有更好的解决思路？**

## 第二种做法：代码无状态，配置信息放到环境变量中。

第一步，保证代码中所有的配置信息都取自环境变量，代码无状态。

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

第二步，为 production, staging-1, staging-2, staging-3, staging-4, staging-5 创建不同的配置文件。

如果你使用的是 AWS，可以把某个环境的配置保存在 [Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html)，敏感信息可以保存在 [AWS secret manager](https://aws.amazon.com/secrets-manager/)。

```
# 创建 staging-1 的配置信息
aws ssm put-parameter \
    --name "staging-1-configuration" \
    --value "parameter-value" \
    --type String \
    --tags "DB_HOST=xxx,DB_USER=xxx,DB_PASSWORD=xxx,REDIS_HOST=xxx,GOOGLE_OAUTH_URL=xxxx"
```

如果你是用的是 Kubernetes，可以把某个环境的配置保存在 [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/)，敏感信息保存在 [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) 中。

```
kubectl create configmap staging1-config-map \
  --from-literral="DB_HOST=mydb.1234567890.us-east-1.rds.amazonaws.com" \
  --from-literral="DB_USER=postgres" \
  --from-literral="DB_PASSWORD=DdQDQ2RgmRJje9CH1cqn"
```


第三步，无论是 production, staging-1， staging-2， staging-3， staging-4，staging-5，还是将来的 staging-100，都共用 `config/environments/production.rb`，换言之各环境 `Rails.env` 返回的永远是 "production"。

![](/media/files/2022/2022-08-12_14-07-10-production-yml.jpg)

第四步： 代码 + 不同的配置 = 不同的环境。

如果你使用的是 Capistrano 部署代码，那么

```
Code + 不同配置 = 不同的环境
```

如果你使用的是 Docker 部署，那么：

```
Docker Image + 不同配置 = 不同的环境。
```

如果你使用的是 Kubenetes，将不同配置文件注入到了Pod中，就搭起了不同的环境。

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

它省时省力。创建环境，只需要准备一份新的配置文件就可以。

这种方案也保证了安全。代码中不包含机密信息，即使代码泄露也不会扩大风险。不同环境配置文件可以设置不同的访问权限，比如仅仅允许 DevOps 团队访问 production 的配置信息。

最后，所有环境的 Rails.env 都是 production，它消除了不同环境的差异（[parity](https://12factor.net/dev-prod-parity)）。


### 第二种方案的缺点

NewRelic, Datadog, Sentry 等监控工具默认会通过 `Rails.env`来读取环境信息，并附在监控信息上。当我们在监控工具查看日志，异常，性能报告时可以以此筛选。

![](/media/files/2022/2022-08-12_12-47-48.jpg)

第二种方案中，所有的环境 `Rails.env` 读取的值永远是 "production"。这就导致监控系统中会把各个环境的日志(log)，异常（error)，性能数据(application performance monitoring, apm)都混在 production 下。遇到问题，工程师根本无法区分是哪个环境出了问题。

![](/media/files/2022/2022-08-12_12-56-12-only-production.jpg)


## 如何解决监控工具的问题？

其实 NewRelic, Datadog, Sentry 提供了接口，允许我们自定义环境名称，以 Sentry 举例，它的语法如下：
 
 ```ruby
Sentry.init do |config|
  #...
  config.environment = "你喜欢的环境名"
end
```

我们可以在 production, staging, staging-1 等不同环境中的配置中，引入一个新的环境变量 "DEPLOYMENT_ID" 来声明环境名称。当 Rails 初始化各种监控工具时，读取 ENV["DEPLOYMENT_ID"] 的值作为环境名称。
 
 
拿 staging-1 举例，如果它的配置信息如下：

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
  config.environment = ENV['DEPLOYMENT_ID']
end
```

可以这样配置 Datadog：

```ruby
Datadog.configure do |c|
  # ...
  c.env = ENV['DEPLOYMENT_ID']
end
```

这样就能保证监控工具的正常运转了。


## 设计图

千言万语，不如一个图。

![](/media/files/2022/2022-08-12-codebase-build-config-deploy.png)


## 后记

虽然范例都是 Ruby on Rails 框架，但我觉得这思路也可以扩展到其他语言和Web 框架。

本文中的方案，来自于 Workstream 同事们的实践经验，我只是提笔记录下来，并非我的工作成果。

特别感谢 Louise Xu, Felix Chen, Vincent Huang, Teddy Wang 的审校和反馈。


