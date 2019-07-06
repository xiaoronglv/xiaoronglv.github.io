---
title: "Merge sort 性能瓶颈分析"
layout: post
guid: 1HrnDXVXqmAG
date: 2019-07-06 08:50:53
coffee:
tags:
  -
---


## 背景介绍

这份文档是关于 PingCAP talent plan TiDB 方向第一周作业的性能瓶颈分析。

TiDB 方向第一周作业要求：

> 用 Go语言实现一个多路归并的 Merge Sort：内存中有一个无序的 int64 数组，数组中有 16M 个整数，要求使用多个 goroutine 对这个无序数组进行排序，使所有元素按照从小到大排序，排序的性能需要比单线程 Quick Sort 快。[1]


为了完成作业，我一共写了三个版本的 Merge Sort，第一个版本非常慢，花的时间是单线程 QuickSort 3.5 倍。在第二个版本中，我降低了 goroutine 的数量，快了一些。第三个版本中又降低了空间复杂度，更快了一些。

本文描述了使用 pprof 一步步的改进代码，从 v1 改到 v3 的整个过程。


## Profiling Tools

性能分析工具用的是 pprof，分析的指标主要是 cpu 和内存。

我看到有人使用 go-torch 制作了非常漂亮的火焰图。考虑到
 uber 已经将这个项目标记为 archive 状态，不再继续维护，所以我没有投入时间制作火焰图。如果这是加分项，我可以补上。


## 第一个版本: 单纯递归

在第一个版本中（[点击查看详细代码](https://github.com/xiaoronglv/talent-plan-draft/blob/master/mergesort/mergesort-v1/mergesort.go)），我按照经典的递归思路实现 Merge Sort。Benchmark test 结果惨不忍睹。耗时是 NormalSort 的 3.5 倍；内存开销是 NormalSort 的 5700 万倍。

![](/media/files/2019/2019-07-06-v1-benchmark.jpg)


我把这个有趣的结果在 PingCAP talent plan 微信群里分享，大家讨论下来的结果是：Goroutine 太多了。Goroutine 虽然不是物理线程，但是毕竟有开销。设想一下，如果对 16M 的数字排序，在递归的最后一层就有八百万个 Goroutine，再算上上面的23层， Goroutine 实在是太多了。创建和调度应该会吃掉很多资源。

于是我按照 Dave Cheney 在 [《How to write benchmarks in Go》](https://dave.cheney.net/2013/06/30/how-to-write-benchmarks-in-go)中介绍的方法，收集了 BenchmarkMergeSort 这个测试的 profiling 数据，看看到底是怎么回事。

```
go test -run=xxxx -bench=MergeSort -cpuprofile cpu.out -memprofile memory
```

注释：

-run 参数会跑符合正则表达式的测试，所以当指定参数为 `-run=xxxx` 时，就会跳过所有的单元测试。除非真的有个测试叫 xxxx。

-bench，默认情况下并不会跑 benchmark test。所以需要指定要跑的 benchmark。通常大家会这样使用 `-bench=.` 来跑所有的 benchmark。但是在此处我只想收集MergeSort 单个 benchmark 的数据，所以我是用了 `-bench=MergeSort`。


首先看 CPU 的 profiling 数据，查看最耗时的 20 个函数调用。

```
> go tool pprof cpu.out
> top20 --cum
```

里面确实有很多和 Goroutine 调度相关的函数，占用了很多资源。

![image](https://raw.githubusercontent.com/xiaoronglv/xiaoronglv.github.io/1f7ac9ef0f73a2ca2afb07df49141363567a2b19/media/files/2019/2019-07-06_08-35-23-v1-benchmark.jpg)


## 第二个版本: 修改递归的终止条件，降低 Goroutine 数量。

根据 V1 的 profiling 数据，我修改了递归的终止条件(stopping condition)。在递归过程中，如果切分的 sub-list 小于 100,000 个元素时，直接使用sort.Slice(即 quick sort) 排序，不再继续递归。从而避免创建一堆 Goroutine。

这就产生了第二个版本的 MergeSort（[点击链接查看完整代码](https://github.com/xiaoronglv/talent-plan-draft/blob/master/mergesort/mergesort-v2/mergesort.go#L15)）。

```
// Before

func mergeSort(src []int64) {
	if len(src) <= 1 {
		return
	} else {
	   .....
	}

}


// After

func mergeSort(src []int64) {
	if len(src) <= 100000 {
		sort.Slice(src, func(i, j int) bool {
			return src[i] < src[j]
		})
	} else {
	   .....
	}

}

```

修改之后，第二版的 MergeSort 耗时是 NormalSort 的 2/5，蛮有成就感。

```
BenchmarkMergeSort-4     1343951054 ns/op (11.3s)
BenchmarkNormalSort-4    3034909073 ns/op (30.3s)
```

再次收集CPU 和 内存的 profiling 数据，对 CPU 和内存开销分析。

```
go test -run=xxxx -bench=MergeSort -cpuprofile cpu.out -memprofile memory.out
```

### 对 CPU 耗时进行分析


![](https://mednoter.com/media/files/2019/2019-07-05_21-59-06-v2.jpg)


V1 版本中那一堆创建和调度 Goroutine 函数已经消失了，CPU 耗时前位的函数分别是：

1. sort.doPivot_func 21.80% (flat%)
2. mergesort.merge 20.80% (flat%)

`sort.doPivot_func` 是 Golang quick sort 的逻辑，已经有无数人做过性能调优，没有太多优化空间。所以我把焦点放到了 `mergesort.merge` 上。它为什么占了这么多时间？它还可以怎么优化？

mergesort.merge 的 cum% 为 23.81%，flat% 为 20.80%，说明:

- merge 内的 direct operation 占了 20.80%。
- 内部函数调用占了 3.01%

merge 这个函数似乎也没有太多优化空间了。


### 对内存开销的分析

![](https://mednoter.com/media/files/2019/2019-07-05_22-03-12-v2-memory.jpg)

merge 函数的内存开销为 1 个 G。递归时每一层都会创建很多临时的变量，用来储存排序过的数字，这些变量很有可能就是元凶。([点击查看代码](https://github.com/xiaoronglv/talent-plan-draft/blob/master/mergesort/mergesort-v2/mergesort.go#L44)）


### 第三个版本：降低空间复杂度。

于是我开始看网上 C++ 版本的 Merge Sort 是怎么优化空间复杂度的。C++ 版本中，有人一开始会创建一个和 src 相同大小的变量做存储空间，并通过指针的操作在递归的各层复用这临时存储空间。

这条路似乎在 Golang 中也走得通。Golang 的 slice 结构并不能直接储存数据，而是保存在一个内部 Array 中。

Slice 结构包含三部分：

1. 指向 Internal array 的指针
2. capacity 
3. length

![](https://mednoter.com/media/files/2019/2019-07-05-golang-slices-illustration.jpg)

当对 Slice 进行 cut 操作时， Golang 并不会创建一个新的 internal array, 而是直接指向老的 Array。

```
// s1 和 s2 的 internal array 是相同的
s1 = []int{1, 2, 3, 3, 2, 1}
s2 = s1[0:2]
```

所以我就用这种方法来降低空间复杂度：一开始就创建一个和 src 相同长度的 slice（暂且叫做 tmp），也参与到递归之中，不断被切分，充当 merge 函数的临时存储空间。因为不同的 Goroutine 操作的 array 的区间不同，所以彼此之间不会打架，也不需要用锁。

根据这个思路，我写了第三版的代码。（[点击查看代码](https://github.com/xiaoronglv/talent-plan-draft/blob/master/mergesort/mergesort-v3/mergesort.go)）

```
> go test -run=xxxx -bench=.
BenchmarkMergeSort-4      1337075775 ns/op  (13 s)
BenchmarkNormalSort-4     3032019853 ns/op  (30 s)
```
与 V2 的代码比起来，CPU 耗时上没有太多提升。

![](https://mednoter.com/media/files/2019/2019-07-06_07-09-47-v3-memory.jpg)
但是 memory 降了很多。mergeSort 压根没有出现在排行榜里。（mergeSort 是小写字母开头，而不是以大写字母开头的 MergeSort）

![](https://mednoter.com/media/files/2019/2019-07-06_07-15-31-disappear.jpg)
耗内存的大户 mergeSort 直接在svg 的图上消失了。


## 结果对比

三分代码，执行相同的 benchmark test 的 CPU 和内存开销。

go test -run=xxxx -bench=MergeSort  -benchmem

| 代码版本 | ns/op | b/op | allocs/op |
|:--|:--|:--| :--| 
| v1 | 9971333427 (9.9 s) | 3645160336 (3.6 G) | 34112645 |
| V2 | 1343951054 (1.3 s) |  1073859808 (1 G) | 1484 |
| V3 | 1187167764ns (1.1 s)|  134236464 (134 M) | 175 |


## 还未解决的问题

第一，在跑 MergeSort 时，不同的计算机有不同的硬件。cpu 的核数和 Goroutine 的最佳比例是多少，我还不清楚。在 V2 中，递归终止条件为 100,000 ，是我随便取的。

我觉得应该有一个更理想的策略来决定递归的终止条件，而不是随意取一个数字做终止条件。

第二，MergeSort 除了递归的实现方法，还有迭代。我还没有尝试迭代，或许也可以试试，对比一下。



最后，谢谢你花时间阅读这个文档，不足之处，还请指正。
