---
title: "毁了我三观的函数式编程"
layout: post
guid: DaBNgvnB0V1HddbNVO41
date: 2017-06-01 17:21:42
tags:
  - 
---

## 1. 方法(函数)调用

OOP

dog.bark(3)
dog.eat('bread')


Scheme

(bark dog 3)
(eat dog "bread")


## 2. or 的写法

OOP

a or b

Scheme

(or a b)

> What does ( or ... ) do?
> ( or . . . ) asks two questions, one at a time. If the first one is true it stops and answers true. Otherwise it asks the second question and answers with whatever the second question answers.
> From 《The Little Schemer》

Ryan 的问题：

or 是 keyword 还是 function？


## 3. 定义一个方法 rember, 剔除 list 中的一个 member

e.g.

(rember a lat)
where a is mint
and lat is (lamb chops and mint jelly)

=> (lamb chops and jelly)


**我尝试的解决思路**

1. condition 该问哪些问题？
list 可以问的问题有三个方面，car(list), cdr(list), list itself.

2. 如何让它递归下去?

3. 如何在 function 内部保存一个局部变量，保存加工过的 list?

在知道 cons 这个函数的前提下，我抓耳挠腮想了半天。依然写不出这个极其简单的 rember 函数。但为了和正确答案比较，依然写出了这段丑陋的代码 —— 披着 scheme 语法的OOP。

```
new_list = ()

(define rember
  (lambda (a lat)
    ( cond
      (( null? lat ) (quote ()))                # 判断 list 是否为空
      (( else 
      	( cond
           ((eq? (car lat) a) (rember cdr(lat)) # 判断 car
           (else 
               step1: (cons (car lat) new_list) # 把符合条件的 atom 添加到 new_list 中。
               step2: (rember cdr(lat))         # 继续递归下去

```


**答案揭晓**

```
(define rember
  (lambda (a lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) a) (cdr lat))
      (else (cons (car lat) # 太精妙了
                  (rember a (cdr lat)))))))

```

这种实现方式太精妙了，如果某个元素满足条件，如何保留它？

把它cons到一个未知的list上。


最外层的代码执行时，要先依赖最内层里面的代码一层层往外执行，是我太笨了吗？

- old list 迭代顺序从外往内；
- new list 的拼接顺序从内往外；


## 4. 