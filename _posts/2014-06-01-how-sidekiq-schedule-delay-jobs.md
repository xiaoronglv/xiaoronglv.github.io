---
title: How Sidekiq schedule delay jobs
layout: post
guid: 4Rp33A71Jjyv
date: 2014-06-01 09:00:00
tags:
   - Ruby
---

## What' Sidekiq?

[Sidekiq](https://github.com/mperham/sidekiq) is a Simple, efficient background processing for Ruby.

Sidekiq uses threads to handle many jobs at the same time in the same process. It does not require Rails but will integrate tightly with Rails 3/4 to make background processing dead simple.

## How to use Sidekiq?

If we want all dog to eat, there are many ways...

1. create a worker, then processed asyn

        # create workder
        class DogEatWorker
          include Sidekiq::Worker
          def perform(food)
            ...
            # do somethings
          end
        end

        # perform
        DogEatWorker.perform_async("meat")

2. use delay method to push job to Redis

        Dog.delay.eat("meat")

3. use delay_for method, and process it at specific time.

        # 2014-05-30 17:15:08 +0800
        # All dog will eat meat after 1 hour.
        Dog.delay_for(1.hours).eat("meat")


when we use delay_for, how sidekiq schedule this delay job and perform it at specific time?


## Roles

![Sidekiq & Redis]({{ site.url }}/media/files/2014/Jun/1-sidekiq-schedule-jobs.png)

1. Rails is the client, who push jobs to Redis.

2. Redis is the data center, who store the jobs.

3. Sidekiq is the server, who consume schedule jobs per 15min.

## Schedule jobs in Redis

[Redis](http://redis.io) is **key-value** database, keys can contain strings, hashes, lists, sets and sorted sets.

    > redis-cli
    > keys *

    1) “one:retry”
    2) "one:queues"
    3) “one:schedule"

see 'one:schedule'? All schedule jobs are store in this queue.

    > type “one:schedule"
    zset

zset is a collection that contains no duplicate elements **with score**. we can get all memebers of this queue using ZREVRANGE.

    > ZREVRANGE one:schedule 0 -1

    1) "{
        "retry":true,
        "queue":"default",
        "class":"Sidekiq::Extensions::DelayedClass",
        "args":["---n- !ruby/class ‘Dog'n- :eatn- - 113n"],
        "jid":"7c2db0d6a326308a8a85d798",
        "enqueued_at":1401441308.209111
        }”

Ops, only one task we got. The following arguments easy to understand:

* retry: do you want retry this task if it fail?
* queue: queue name you specify
* jid: unique id
* args: let somebody do something
* enqueued_at: it’s a Unix timestamps, which can be tranform to a standard time

Transform...

    # Unix => standard
    > Time.at(1401441308.209111)
    => 2014-05-30 17:15:08 +0800


Wait!

What’s the fucking information of 1 hour?

The delay time has been discarded?

How does Sidekiq know when to run this task?

    > Dog.delay_for(1.hours).eat("meat")

## Source Code

step1: Transform delay/delay_for/delay_until to same data structure

    module Klass
      def sidekiq_delay(options={})
        Proxy.new(DelayedClass, self, options)
      end
      def sidekiq_delay_for(interval, options={})
        Proxy.new(DelayedClass, self, options.merge('at' => Time.now.to_f + interval.to_f))
      end
      def sidekiq_delay_until(timestamp, options={})
        Proxy.new(DelayedClass, self, options.merge('at' => timestamp.to_f))
      end
      alias_method :delay, :sidekiq_delay
      alias_method :delay_for, :sidekiq_delay_for
      alias_method :delay_until, :sidekiq_delay_until
    end

step2: Push to Redis

    def schedule(timestamp, message)
      Sidekiq.redis do |conn|
        conn.zadd(@zset, timestamp.to_f.to_s, Sidekiq.dump_json(message))
      end
    end

# Conclusion

Timestamp is the perform time, be stored as the score of zset! 

So Sidekiq can get all memebers at a set interval.