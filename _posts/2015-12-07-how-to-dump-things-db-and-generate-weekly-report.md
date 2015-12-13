---
title: 自动生成每周 Performance 报表
layout: post
guid: 8n9mm9d5kf4qzppvmgr68ltcu
date: 2015-12-07 00:00:00
tags:
  - 
---
> 对程序员来说，懒是一种值得称颂的美德。 -- Kong Fuzi

![](https://ruby-china-files.b0.upaiyun.com/photo/2015/ae40ebdb841d88f912feec89438cf7d9.jpg)

[Things](https://culturedcode.com/things/) 是Mac 下一个比较常用的 GTD 管理工具， 用了大概2年了，十分顺手，但是有几个需求满足不了我。

1. 我需要每周有个概览，做 self performance
2. 我需要查看循环任务的完成度，评估自己的表现
3. 我需要查看每个 tag 下任务的完成度，评估自己的表现

以前是手工统计，费时费力。无意间看到它的数据库是 sqlite，而且没加密，于是抽几个小时写了个脚本，自动生成上一周的任务统计报告。实现的比较粗糙，有需要的同学可以拿去再重构。


# 效果图

![](https://ruby-china-files.b0.upaiyun.com/photo/2015/8b67151d7d5577a469cbdab5e9240294.jpg)

![](https://ruby-china-files.b0.upaiyun.com/photo/2015/2105bfaa5b12be381ff74a07242a4343.jpg)

![](https://ruby-china-files.b0.upaiyun.com/photo/2015/36da04812e4fd78c8a8801ecc640dbde.jpg)

## 脚本

```ruby
#things.rb

require 'fileutils'
require 'date'
# please install the following 5 gems.
require 'sqlite3'
require 'active_record'
require 'hirb'
require 'hirb-unicode'
require 'rainbow'

# copy sqlite db file to tmp
src = "/Users/i319092/Library/Containers/com.culturedcode.things/Data/Library/Application Support/Cultured Code/Things/ThingsLibrary.db"
dest = '/tmp'
FileUtils.cp_r src, dest, remove_destination: true


# connect database
ActiveRecord::Base.establish_connection(
  "adapter" => "sqlite3",
  "database"  => "/tmp/ThingsLibrary.db"
)
ActiveRecord::Base.logger = nil

# this week's report
now = Date.today
this_monday = now - (now.wday - 1) % 7
this_sunday = this_monday + 6

# last week's report
last_monday = this_monday - 7
last_sunday = last_monday + 6



start_date = last_monday
end_date = last_sunday



# seperate models
setup = []
setup << "CREATE TABLE  `tags` as select Z_PK as id, ZTITLE as title from ZTHING where ZTYPE1=0"
setup << "CREATE TABLE  `task_tags` as select Z_12NOTES as task_id, Z_14TAGS as tag_id from Z_12TAGS"

# convert dateime format
setup << "update ZTHING set
  ZCREATIONDATE = datetime(ZCREATIONDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZSTOPPEDDATE  = datetime(ZSTOPPEDDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZTICKLEDATE   = datetime(ZTICKLEDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZDELEGATEDDATE   = datetime(ZDELEGATEDDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZINSTANCECREATIONSTARTDATE   = datetime(ZINSTANCECREATIONSTARTDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZNOTIFIEDDATE   = datetime(ZNOTIFIEDDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZRECEIVEDDATE   = datetime(ZRECEIVEDDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZUSEDDATE   = datetime(ZUSEDDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZUSERMODIFICATIONDATE   = datetime(ZUSERMODIFICATIONDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZDUEDATE      = date(ZDUEDATE, 'unixepoch', '+31 years', 'localtime', '+1 day'),
  ZSTARTDATE    = date(ZSTARTDATE, 'unixepoch', '+31 years', 'localtime','+1 day'),
  ZDUEDATEWITHOFFSET   = date(ZDUEDATEWITHOFFSET, 'unixepoch', '+31 years', 'localtime','+1 day')
"
setup.each do |sql| 
  ActiveRecord::Base.connection.select_all(sql)
end

# Repeated tasks report
# | Routine                                                          |  Success  |  Failed  |  Total |
# |----------------------------------------------------------------- | --------- | -------- | -------|
# | [Night] Love actions |  prepare breakfast | prepare dinner       |  7        |  0       |  7     |
# | Drink a cup of water                                             |  7        |  0       |  7     |
# | Drink a cup of water                                             |  7        |  0       |  7     |
# | Pray                                                             |  6        |  1       |  7     |
# | Anki: Review quiet                                               |  6        |  1       |  7     |
# | [Night] Love words                                               |  6        |  1       |  7     |

repeated_tasks =  "select 
  ZTITLE as 'Routine',
  count(case when ZSTATUS = '3' then 1 else null end) as Success,
  count(case when ZSTATUS = '2' then 1 else null end) as Failed,
  count(*) as Total
  from ZTHING as tasks
  where 
    ZREPEATINGTEMPLATE is not null and
    ZSTARTDATE >= '#{start_date}' and
    ZSTARTDATE <= '#{end_date}'
  GROUP BY ZREPEATINGTEMPLATE
  ORDER BY Success DESC"


puts "\n" * 3 + "# Part1: Repeated tasks report"
results = ActiveRecord::Base.connection.select_all(repeated_tasks)
puts Hirb::Helpers::Table.render(results.rows, :change_fields => results.columns, :markdown => true).gsub("Yes", Rainbow("Yes").green).gsub("No", Rainbow("No").red)


# Tag summary report
# | Tag       |  Success  |  Failed  |  Total |
# |---------- | --------- | -------- | -------|
# | Myself    |  37       |  25      |  62    |
# | Learning  |  20       |  8       |  29    |
# | Family    |  17       |  2       |  19    |
# | God       |  11       |  4       |  15    |
# | Church    |  5        |  1       |  6     |
# | Work      |  2        |  0       |  2     |

tag_summary =  "select 
    tags.title as Tag,
    count(case when tasks.ZSTATUS = '3' then 1 else null end) as Success,
    count(case when tasks.ZSTATUS = '2' then 1 else null end) as Failed,
    count(*) as Total
    from ZTHING as tasks
  JOIN task_tags
  ON tasks.Z_PK = task_tags.task_id
  JOIN tags
  on task_tags.tag_id = tags.id
  where 
    tasks.ZSTARTDATE >= '#{start_date}' and
    tasks.ZSTARTDATE <= '#{end_date}'
  GROUP BY task_tags.tag_id
  ORDER BY Success DESC"


puts "\n" * 3 + "#Part 2: Tag summary report"
results = ActiveRecord::Base.connection.select_all(tag_summary)
puts Hirb::Helpers::Table.render(results.rows, :change_fields => results.columns, :markdown => true).gsub("Yes", Rainbow("Yes").green).gsub("No", Rainbow("No").red)


# Every Tag's Report
#
# God
# | Title                                     |  Start       |  End         |  Completed  |  Tag |
# |------------------------------------------ | ------------ | ------------ | ----------- | -----|
# | Pray                                      |  2015-11-22  |  2015-11-22  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-22  |  2015-11-22  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-21  |  2015-11-21  |  Yes        |  God |
# | Pray                                      |  2015-11-21  |  2015-11-21  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-20  |  2015-11-20  |  Yes        |  God |
# | Pray                                      |  2015-11-20  |  2015-11-20  |  Yes        |  God |
# | Pray                                      |  2015-11-19  |  2015-11-19  |  Yes        |  God |
# | Collect others pray Items to my Evernote  |  2015-11-19  |  2015-11-19  |  No         |  God |
# | Study Bible or Related book.              |  2015-11-19  |  2015-11-20  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-18  |  2015-11-19  |  No         |  God |
# | Pray                                      |  2015-11-18  |  2015-11-19  |  No         |  God |
# | Pray                                      |  2015-11-17  |  2015-11-17  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-17  |  2015-11-17  |  Yes        |  God |
# | Study Bible or Related book.              |  2015-11-16  |  2015-11-17  |  No         |  God |
# | Pray                                      |  2015-11-16  |  2015-11-16  |  Yes        |  God |

puts "\n" * 3 + "#Part3: Report of every tag"

tags = %w(God Myself Family Learning Work Church Others)
tags.each do |tag|
  sql = "select 
      tasks.ZTITLE as Title,
      tasks.ZSTARTDATE as Start,
      date(tasks.ZSTOPPEDDATE) as End,
      (case when tasks.ZSTATUS = '3' then 'Yes' when tasks.ZSTATUS = '2' then 'No' else null end)  as Completed,
      tags.title as Tag
      from ZTHING as tasks
    JOIN task_tags
    ON tasks.Z_PK = task_tags.task_id
    JOIN tags
    on task_tags.tag_id = tags.id
    where 
      tasks.ZSTARTDATE >= '#{start_date}' and
      tasks.ZSTARTDATE <= '#{end_date}' and
      tags.title = '#{tag}'
    ORDER BY ZSTARTDATE DESC"

  puts "\n" * 3 + "Tag: " + tag
  results = ActiveRecord::Base.connection.select_all(sql)
  puts Hirb::Helpers::Table.render(results.rows, :change_fields => results.columns, :markdown => true).gsub("Yes", Rainbow("Yes").green).gsub("No", Rainbow("No").red)
end

```

这个脚本的使用方法：

```
# 安装依赖
gem install 'fileutils'
gem install 'date'
gem install 'sqlite3'
gem install 'active_record'
gem install 'hirb'
gem install 'hirb-unicode'
gem install 'rainbow'

# 执行脚本
ruby things.rb
```


## 几个吐槽的地方

Things 的数据库设计的真是烂啊，tag、area、project 全部放到一张表里 ZTHING。

为了便于写 sql，我只好:

0. 拷贝数据库文件在 /tmp，用备份数据库加工数据
1. 单独创建了 tags 表 
2. 单独创建了 task_tags 表，一个关系表


## Reference

1. 本文的代码片段下载 [gist](https://gist.github.com/xiaoronglv/eb9356e40f0a28ca6d53)

2. 范例1：[2015-12-13 Weekly Performance](http://mednoter.com/weekly-performance-2015-12-13.html)

3. 范例2：[2015-12-06 Weekly Performance](http://mednoter.com/weekly-performance-2015-12-06.html)
