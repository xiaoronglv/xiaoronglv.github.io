Sidekiq的测试怎么写


## 官方文档提供了三种测试模式

<pre><code>
require 'sidekiq/testing'
Sidekiq::Testing.fake!
Sidekiq::Testing.inline!
Sidekiq::Testing.disable!
</code></pre>


### Sidekiq::Testing.fake!（不用redis）

在test文件的头部添加这段代码后，若test case中有异步任务，sidekiq不会将异步任务保存在redis中，而是保存在一个数组 `jobs` 中。 

<pre><code>
require 'sidekiq/testing'
Sidekiq::Testing.fake!
</code></pre>

test case 范例

<pre><code>
# jobs中的异步任务数为0
assert_equal 0, HardWorker.jobs.size
HardWorker.perform_async(1, 2)

# jobs中的异步任务数为1
assert_equal 1, HardWorker.jobs.size

</code></pre>

使用drain方法，可以执行所有的异步任务

<pre><code>
# 生成2个异步任务
HardWorker.perform_async(1, 2)
HardWorker.perform_async(2, 3)
assert_equal 2, HardWorker.jobs.size

# 执行所有异步任务
HardWorker.drain

# 异步任务总数为0
assert_equal 0, HardWorker.jobs.size
</code></pre>

如果不打算执行，只想清空jobs，可以使用clear方法

<pre><code>
# 生成异步任务
HardWorker.perform_async(1, 2)

# 清空jobs中的所有异步任务
HardWorker.jobs.clear

assert_equal 0, HardWorker.jobs.size
</code></pre>

### Sidekiq::Testing.inline!（同步执行）

在test文件的头部添加这段代码后，若test case中有异步任务，都会修改为同步执行。

<pre><code>
require 'sidekiq/testing'
Sidekiq::Testing.inline!
</code></pre>

### Sidekiq::Testing.disable!（禁用所有异步任务）

在test文件的头部添加这段代码后，若test case中有异步任务，都会跳过。

<pre><code>
require 'sidekiq/testing'
Sidekiq::Testing.disable!
</code></pre>

## 直接测试worker

可以直接创建一个worker对象，然后执行。

<pre><code>
work = HardWorker.new
work.perform(1, 2)
</code></pre>

## Reference

1. [sidekiq Testing](https://github.com/mperham/sidekiq/wiki/Testing)