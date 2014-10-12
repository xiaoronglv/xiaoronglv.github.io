---
title: FactoryGirl tips
layout: post
guid: ymqhmc33p4pj
date: 2014-10-12 22:25:00
tags:
   - Ruby
---

我刚刚发现了 FactoryGirl 的两个技巧: trait 和 association。真好用呀，让我的测试代码量少了 1/5。

通过两个 case 来说明一下心得体会。


## 案例 1 创建不同状态的帖子

**Before**

    FactoryGirl.define do
      # 论坛帖子
      factory :post do
        title 'New post'
        body  'I love jesus'
      end

      # 已发布的帖子
      factory :published_post do
        title 'New post'
        body  'I love jesus'
        published_at Date.new(2012, 12, 3)
      end

      # 草稿状态的帖子
      factory :draft_post do
        title 'New post'
        body  'I love jesus'
        published_at nil
      end
    end

这样写有很多弊端

1. 代码重复严重，Post 增加一个属性后，需要更新四个 factory

2. 每增加一个状态，就需要新创建一个 factory

3. 太丑了！


**Refactor** (Good) 

我们可以使用继承 [inheritance](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md#inheritance) 来减少重复代码。

    FactoryGirl.define do
      # 帖子
      factory :post do
        title 'New post'
        body  'I love jesus'

        # 已发布的帖子
        factory :published_post do
          published_at Date.new(2012, 12, 3)
        end

        # 草稿状态的帖子
        factory :draft_post do
          published_at nil
        end
      end
    end

重构后，published_post、draft_post 可以从 post 继承 title、body 两个属性，避免了重复代码。

但是还是有缺点

1. factory (比如：published_post) 的名字越来越长，直逼 Object C 变量的长度。

2. 条件很多时，使用继承压根不省事儿。
    * draft_post
    * draft_post_with_star
    * draft_post_without_star
    * publish_post
    * publish_post_with_star
    * publish_post_without_star

**Refactor**(Better) 

我们可以使用 Trait 做进一步的优化。[Trait](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md#traits) 像 Ruby 中可以复用的 Module。

    FactoryGirl.define do
      # 帖子
      factory :post do
        title 'New post'
        body  'I love jesus'

        # 已发布的帖子
        trait :published do
          published_at Date.new(2012, 12, 3)
        end

        # 草稿状态的帖子
        trait :draft do
          published_at nil
        end

        # 被标记为精华
        trait :star do
          star       true
          star_count 50
        end

        # 未被标记为精华
        trait :without_star do
          star       false
          star_count 2
        end
      end
    end


可以按需搭配构造不同 factory

    # test/model/post_test.rb
    # 创建一个已发布，加精的 post
    
    FactoryGirl.create :post, :published, :star

    # 创建一个草稿状态的 post
    FactoryGirl.create :post, :draft



## 案例 2 创建 1:n 关系

我们在写测试的时候，经常要构造 1:n 这种关系的数据，我以前会把相关的逻辑放到测试文件中。


**Before**
    
    # test/model/post_test.rb
    post = create :post
    comments = create_list :comments, 3, post_id: post.id
    post.reload
    
    
遇到这种情况，可以使用 [ignored attributes 和 callback](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md#associations) 把构造关系的逻辑放到 factory 中。

**Refactor**


    FactoryGirl.define do
      factory :post do
        title 'New post'
        body  'I love jesus'


        # 评论
        trait :without_comments do
          ignore do
            number_of_comments 3
          end

          after :create do |post, evaluator|
            FactoryGirl.create_list :comment, evaluator.number_of_comments, :post => post
          end
        end
      end
    end


重构完后，所有的关系都被封装在 factory 中，我在 test case 可以肆意使用各种姿势构造数据了。

    # test/model/post_test.rb
    
    # 创建带评论的帖子
    FactoryGirl.create :post, :with_comments
    
    # 创建带4个评论的帖子
    FactoryGirl.create :post, :with_comments, :number_of_comments => 4



## 参考资料

[FactoryGirl Guide](https://github.com/thoughtbot/factory_girl/blob/master/GETTING_STARTED.md)

[FactoryGirl Tips and Tricks](http://arjanvandergaag.nl/blog/factory_girl_tips.html)


