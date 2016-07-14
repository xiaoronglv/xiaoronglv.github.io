---
title: Migrate from Rspec to Minitest with Rails
layout: post
guid: zdgllm36pxpy
date: 2014-09-24 22:23:00
tags:
   - 
---

New Rspec has released, with lots of DSL changes. Every rspecer is painfull. ^_^

Minitest is so lightweight, cute and simple. Why was I so stuip to choose Rspec?

This article will show you how to migrate from rspec to minitest.

## Gemfile

Add 'minitest_rails' and 'mocha' to gemfile


```ruby
# Rails4 use minitest as default testing framework, if your rails version is greater than 4.0, you can skip this step.
gem "minitest"
gem "minitest-rails"

```

MiniTest::Mock is not so power, we use Mocha instead.

```ruby
gem "mocha", :require => false
```

Gem Turn is great way to view test results. you can see exact result of every test (Success, Faiure and Why). 

```ruby
gem "turn", :require => false
```

## test_helper.rb

1. After installed 'minitest-rails', you can run `rails generate minitest:install` to add test_helper.rb to the test directory.

2. require


```ruby
require "minitest/autorun"
require "minitest/rails"
require "mocha/setup"
```


## FactoryGirl

Don't worry. Factory can find all the factories under spec/. old factories will be load when you run `rake test`. But you'd better put new factories into test/.


## Rakefile

Add pattern in you Rakefile, additional testing tasks defined in "test/**/*_test.rb" will be evaluated when you run `Rake test`

```ruby
Rake::TestTask.new do |t|
  t.libs << "test"
  t.test_files = FileList['test/**/*_test.rb']
  t.verbose = true
end
```

## Bingo, it works

run `rake test`
