---
title: "对 Linux route table 的几个疑惑以及思考过程"
layout: post
guid: c2QGi9wjSUnF
date: 2022-04-07 21:00:00
tags:
  -
---

Linux 有很多网络概念，比如 ARP，Gateway，Router，Network Interface，IP/TCP protocol的header，Netfilter, Route table。

这些都是 Kubernetes 的前置知识，如果没搞懂，在学习 Kubernetes 时往往会被卡住。其中 Route table 卡了我一个月的时间。

`ip route add` 用于添加路由表规则

```
$ ip route add <network_ip>/<cidr> via <gateway_ip>

# Example
$ ip route add 10.0.3.0/24 via 10.0.3.1
```

以下是个范例，当 IP packet 的 destination IP 在10.0.3.0/24这个网段时，走 10.0.3.1 这个网关。

但是我在看这个命令时，产生了以下疑惑：

**第一个疑惑：在路由表添加记录时，只指定了目标IP段 和 Gateway，当 Linux 在按照 "10.0.3.0/24 via 10.0.3.1"这个规则去分发包裹时，它是怎么分发的呢？**

就像一趟航班 (packet) 要到巴黎 (gateway), 它到底是怎么一步步过去的呢？走了哪个机场，哪个跑道？


**第二个疑惑：一台电脑可能有多个 Network Interface，Linux 是怎么根据 Gateway 找到对应的 Network Interface 呢？**

除非 Gateway 和 Network Interface 有一一对应的关系。每次按照这个流程投递包裹：

1. 前往 10.0.3.4 的 packet，满足路由规则 "10.0.3.0/24 via 10.0.3.1"
2. 所以会被发送到 Gateway 10.0.3.1
3. Gateway 对应的 Network Interface 是 eth0
3. 所以 packet 会通过底层的 Network Interface eth0 投递。

**这引出我的第二个疑惑，是谁提前配置好 Gateway 和 Network Interface 的对应关系呢？**

上海就有两个机场，政府规定：
- 虹桥机场负责国内航班
- 浦东国际机场负责国际航班和国内航班。

是谁规定好"前往巴黎的航班"请走 Network Interface "eth0"? 又是谁规定好"前往上海的航班"请走 Network Interface "eth1"？

然后随后的疑惑开始指数式的爆炸。

- Gateway 的准确定义是什么？
- Network Interface 的准确定义是什么？有几种？
- Gateway 和 network interface 到底什么关系？
- 当我在操作系统中添加 *Gateway * 时，它属于操作系统，还是属于 Network Interface？
- Network Interface 和 IP 是什么关系？ 1:1 还是 1:N?
- 我可以随便给 Network 配置 IP 吗？可以配置一个乱七八糟的 IP吗？
- Gateway 和 Router 的区别是什么？
- IPtables 和 Route table 是如何协作的？

这些问题纠缠在一起，让我越陷越深。



## 理解问题的新思路

ip route add <network_ip>/<cidr> via <gateway_ip> dev <network_card_name>


By default, if you don’t specify any network device, your first network card, your local loopback excluded, will be selected.


Before

我们只把 gateway 当作 target，即 Packet 的下一站。我理解路由表的方式是 ”当IP 满足条件时，投递给 Gateway“


After

但完整的 Target 其实是两部分组成

- Gateway
- Network Interface

路由表中的每条记录的含义是 ”当 IP 满足该条件时，==通过某个 network interface== 投递给某个Gateway。“



## Reference

1. 









如果把 Packet 当成一个包裹，那么这个包裹的下一站是 Gateway 呢，还是 network interface？

Gateway `10.0.3.1` 的这个配置，是


gateway 地址 是 network interface 的配置，还是操作系统层面的设置？

OS

network interface

IP config

will ip aliasing create a new network interface?




## Reference

[serverFault: Can a single network card have 2 IP addresses?](https://serverfault.com/questions/312221/can-a-single-network-card-have-2-ip-addresses)
