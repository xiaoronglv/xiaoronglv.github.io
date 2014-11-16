

## 方案一  概览 + 流水表

    class User < ActiveRecord::Base

      # name: string, 用户名
      # checkin_days: integer, 用户签到总次数
      # continuous_checkin_days: integer, 用户连续签到天数
      # last_checked_at: DateTime, 用户最后一次签到日期

      has_many :check_ins
    end



    class CheckIns < ActiveRecord::Base

      # user_id: 用户 id
      # checked_in_at: 签到日期

      belongs_to :user
    end



| User                    | 
| ----------------------- | 
| id                      |
| name                    | 
| checkin_days            | 
| continuous_checkin_days |
| last_checked_at         | 


| CheckIns        | 
| --------------- | 
| id              | 
| user_id         | 
| checked_in_at   | 


每天凌晨执行一个 rake 任务，若用户最后一次签到日期在前天，则把用户的连续登录天数（continuous_checkin_days）重置为 0。针对中小型网站，这种方案没有太大问题。

缺点：

我们的日活跃有几十万用户，如果日增 30 万条记录，很快会把流水表撑爆掉。


## 方案二  只保留概览数据

思来想去，反正签到的业务不涉及金钱，出错一两次老板也不会拿砍刀削我。日增 30 万的的流水表貌似没有存在的价值，反而把数据库搞得很累。

只保留一张概览表，每天凌晨将最后签到日期为前天的用户的 「continuous_checkin_days」归零。


| User                    | 
| ----------------------- | 
| id                      |
| name                    | 
| checkin_days            | 
| continuous_checkin_days |
| last_checked_at         | 



## 方案三 Redis 

无意间看到 @zgm 的代码，灵光一闪，这些计数资料一股脑扔到 redis 中算了。

代码中用到了 redis-object gem.





