2022-03-28-naming-of-iptables.md



iptable 的科普资料汗牛充栋，我就不赘述了。通常大家都会有5个table。

FILTER
NAT
MANGLE




大家就会想当然的认为，rule 是保存在 table 中。


可是过一会，又回引入 chain的概念。





这篇文章主要介绍一个令新手非常困惑的命名问题。这个问题浪费了我2周的时间，总算搞懂了。




table 到底有什么用？

在命令行里似乎也不需要制定 table 和 chain ？

iptables -A INPUT -p udp --dport 67 -j ACCEPT

怎么理解table？

table 和 chain 有什么关系？



## 吐槽



stackoverflow 上也有人质疑这种命名方式，非常的让人困惑。


其实完全可以把当前设计的chain 叫做 table，把当前设计的chain，称之为 group，priority，channel，就会让人更容易理解一些。

如果极端一点，甚至不暴露这个概念给用户就可以。这样大家在理解这个概念的时候，就不会把 chain 和 table 当成一个矩阵来理解。



## 如何更简单的理解

chain 是 Rule 的容器。

table 其实是rule的用途，可以当作一个 tag 来理解。


