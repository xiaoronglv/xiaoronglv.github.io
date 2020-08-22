---
title: "使用 Rails Generator 提高公司的开发效率"
layout: post
guid: VjpvO6uC3EMpL5N2RN1d
date: 2020-08-22 08:23:13
coffee:
tags:
  -
---

Rails 有非常多的约定，比如当我用这行命令创建一个资源，框架会帮我做很多决策，也会帮我创建相应的代码文件。

`rails generate resource post title:string description:text`

- 数据库的表的命名为复数 `posts`
- 主键是 `id`
- model 放在 app/models/post.rb
- controller 放在 app/controllers/posts_controller.rb

公共的约定降低了初学者的门槛，一个工程师并不需要懂太多计算机知识，也不需要花费太多时间去配置框架的方方面面，就可以搭建出一个像样的网站。


## 问题

在现实世界中，很多公司还会在 Rails 框架之上再抽象出一层业务框架。新人要开展工作，不但要精通技术，还要把一堆模型的逻辑关系理清楚，即使是有经验的工程师也会怀疑人生：“我是来写代码的，还是来读历史的？”

拿我公司的一个子产品**入职流程**来举例，这个产品帮助 HR 管理入职流程。HR 可以像搭积木一样搭建各种入职流程，“产品经理入职流程”，“程序员入职流程”，“设计师入职流程” 等等。

- 收集员工的基本信息。姓名，性别，紧急联系人，家庭住址等个人信息。
- W4 模块。美国的税务文档，员工填写税务相关的信息给雇主，方便雇主帮自己缴税。
- ADP 集成模块。我们把员工的个人信息，推送给雇主的其他人力资源软件。
- WOTC 集成模块。我们把员工的个人信息，推送给政府，判断是否可以减税。
- 工资单模块。把员工信息推送给雇主正在使用的工资单应用，比如 Paychex。（在美国，各个行业非常细分，甚至有单独的企业专门做工资单。）

当有新员工入职时，HR 选择一个合适的流程让他们入职，员工一步步完成任务，就可以顺利的入职了。

我的主要的工作就是开发一个个的模块（积木）。我自认为整个业务代码的设计还是比较符合 Open–closed 的原则。添加一个模块几乎不需要修改老代码，主要就是创建子类(Concrete class)，增加和某个模块相关的特殊 API，测试，locale 翻译文件，配置文件等等。

表面上不复杂，但因为涉及的业务模型太多，其他工程师没有一两个月根本插不上手，主要有这几个原因：

- 我自认为设计非常直白，其实晦涩难懂。只不过因为是我写的，所以我知道上下文。对于别人则是天书。
- 文档质量不好，或很快过期。
- 大家撸起袖子就干，很少读文档，都是遇到问题再去翻文档。

**如果有些工作是重复的，有套路的，有什么办法让降低业务开发的门槛，提高开发效率？**

很多人首先想到的办法是写文档，事无巨细都写到文档里。让后人照着做就可以了。

最人性的办法是安排一个 mentor，手把手告诉新人: ”Hi，虽然有30个表，但是你需要加的只有这三个文件。这个，这个，还有那个。。。“

最不近人情的办法，写非常完备的测试。如果别人不按规矩提交代码，就让集成测试(CI)直接挂掉。这加剧了别人的痛苦，但是没提高别人的工作效率。

今天我想介绍的是另外一种办法 Rails generator。

## 什么是 Rails Generator？

我们每天的日常工作都会用到它。

```
# 创建一个 rails 项目
rails new blog

# 创建一个 db migration 文件
rails generate migration AddNameToUser name:string

# 创建一个 model
rails generate model post title:string description:text

# 创建一个 resource 对应的全套文件，model，view，路由。
rails generate resource product slug:string name:string
```

简单来讲，一个 generator 内部定义了模版文件，当我们运行时，它会基于模版文件生成代码文件，然后复制到目标目录中。

下面我给大家介绍制作一个简单 generator 的过程。这个 generator 的用途是，当工程师执行 `rails generate onboarding_module  paychex` 时：

- 自动创建好几个子类
- 自动创建这几个子类的 rspec 测试文件
- 自动创建 FactoryBot 的文件
- 创建对应的 mapper，做数据转换
- 渲染一段配置，添加到某个 yml 的尾部
- 在某个文件的某一行代码里，加一个常数。

## 方法

### 第一步，创建一个 Generator

Rails 有一个专门的 generator 来创建 generator。运行这个命令来创建我自己的 onbaording_module 生成器。

```
bin/rails generate generator onboarding_module
```

它会帮我创建以下目录和文件

```
# 生成器的主要逻辑放在这个文件里
create  lib/generators/onboarding_module/onboarding_module_generator.rb
# 帮助文档
create  lib/generators/onboarding_module/USAGE
# 模版文件
create  lib/generators/onboarding_module/templates
```


### 第二步，在 template 目录下，创建模版文件。

```
└── onboarding_module
    ├── USAGE
    ├── onboarding_module_generator.rb
    └── templates 👈👈👈 看这里，这些是我创建的模版。
        ├── factories
        │   ├── template_module.rb.erb
        │   └── employee_module.rb.erb
        ├── mapper.rb.erb
        ├── models
        │   ├── template_module.rb.erb
        │   └── employee_module.rb.erb
        ├── module.yml.erb
        └── spec
            ├── template_module_spec.rb.erb
            └── employee_module_spec.rb.erb
```

这些模版都是 erb 文件，内部嵌有实例变量，`if/else`，我可以动态的生成各种代码文件。

```
# templates/factories/template_module.rb.erb
FactoryBot.define do
  factory :company_<%= @module_name -%>_module, class: Onboarding::<%= @module_name.camelize -%>Module do
    company_onboarding_process { nil }
    deleted_at { nil }
    module_type { '<%= @module_name %>' }
    name { "TODO: Write this module's name" }
    sequence(:sequence) { |n| n - 1 }
    config { }

    trait :with_dependencies do
      after(:create) do |mod|
        <%- @actions.each do |action| -%>
        create(:company_onboarding_action, :<%= action -%>, company_onboarding_module: mod)
        <%- end -%>
        mod.reload
      end
    end # end of trait

  end # end of factory
end # end of top
```

### 第三步，生成代码。

Rails generator 提供了很多接口来操作文件，我们可以通过调用不同接口来加工文件。

一， `template` 方法。它可以渲染 erb 模版，生成对应的代码文件，并放到目标位置。我用这种方式生成 model, factories, rspec 测试。

```
template(
  'models/company_module.rb.erb', 
  'app/models/#{file_name}_module.rb'
  )
end
```

二， `inject_into_file` ，在文件的某个位置插入一段代码。
 
在目标文件 foo.rb 做作一个标记
 
 ```
ModuleList = [
  # 我的标记
  'w4_module',
  'paychex_module',
]
```
 
在这个标记的后面插入目标文件

``` 
inject_into_file 'app/models/foo.rb', after: "# 我的标记" do
  'new_module',
end
```

执行后的效果

 ```
ModuleList = [
  # 我的标记
  'new_module'  # 👈 看这里，这一行是新添加的
  'w4_module',
  'paychex_module',
]
```


Rails generator 提供的接口非常多，可以添加 gem, 替换文件内容，在这里就不一一涵盖了。你可以在 [官方文档](https://ruby-china.github.io/rails-guides/generators.html#generator-methods) 查看。


## 效果

一个不熟悉业务的工程师开展工作，只需要运行 `rails generate onboarding_module  paychex` 就可以搭好业务架子，然后填上自己的业务代码就搞定了。

这是效果图

![](/media/files/2020/2020-08-22_16-31-25.jpg)









