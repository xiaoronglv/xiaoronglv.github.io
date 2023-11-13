---
title: "The Little Schemer: The Ten Commandments with Notes"
layout: post
guid: 4Rz3JwP7Yda232Jw
date: 2017-05-29 14:19:10
tags:
  - 
---

![](/media/files/2017/2017-05-29-murderer.jpg)

# The Ten Commandments

## The First Commandment

When recurring on a list of atoms, `lat`, ask two questions about it: `(null? lat)` and `else`.

When recurring on a number, `n`, ask two questions about it: `(zero? n)` and `else`.

When recurring on a list of S-expressions, `l`, ask three question about it: `(null? l)`, `(atom? (car l))`, and `else`.


Ryan's Questions

### 1.1 what is atoms?

From [Jack Baskin School Of Engineering](https://classes.soe.ucsc.edu/cmps112/Spring03/languages/scheme/SchemeTutorialB.html)

An atom is a fundamental unit in Scheme, which consists of:

1. number, `1`, `-123`
2. string, `"I love God"`
3. symbols, `'a`
4. boolean: True and False. In Scheme, these are represented as #t and #f


### 1.2 what does `lat` mean?

lat means list of atoms. 

lat? is a function which returns #t if it is the empty list or a list whose every element is an atom. 

### 1.3 what does `(null? lat)` mean?

### 1.4 what does `else` mean?

### 1.5 what does `n` mean?

### 1.6 what does `l` mean?

a variable

## 1.7 what does `car` mean?

| car | cdr |
|:--|:--|
| head | tail |
| first | rest |

![](/media/files/2017/2017-05-29-listmonster.png)


### 1.8 what does S-expressions (symbolic expression) mean?

S-expression is a data structure for nested list data.

JSON representing dictionary

`'{"oranges": 2, "apples": 6, "pears": 5}'`


S-expressions representing a tree

`'((ORANGES 2) (APPLES 6) (PEARS 5))`





### 1.9 What's the intention of S-expressions?


### 1.10 What's the difference bewteen S-expression and list?


## The Second Commandment

Use `cons` to build lists.


### 2.1 What is cons?

In computer programming, cons (/ˈkɒnz/ or /ˈkɒns/) is a fundamental function in most dialects of the Lisp programming language. cons constructs memory objects which hold two values or pointers to values.

From [Wikipedia](https://en.wikipedia.org/wiki/Cons)


### 2.2 What is list?

From [Jack Baskin School Of Engineering](https://classes.soe.ucsc.edu/cmps112/Spring03/languages/scheme/SchemeTutorialB.html)

A list consists of space-seperated objects between parenthes.

`(0, 2, 4, 6, 8)`


the structure of list

![](/media/files/2017/2017-05-29-structure-of-list.jpg)

![](/media/files/2017/2017-05-29-listmonster.png)



## The Third Commandment

When building a list, describe the first typical element, and then cons it onto the natural recursion.


Ryan's Questions

1. What does `natural recursion` mean?

https://stackoverflow.com/questions/32260444/what-is-the-definition-of-natural-recursion

2. notes

https://github.com/offtherailz/notes/blob/master/scheme/little-schemer/little-schemer.md#chapter-3-cons-the-magnificent


## The Fourth Commandment

Always change at least one argument while recurring.

* When recurring on a list of atoms, `lat`, use `(cdr lat)`.
* When recurring on a number, `n`, use `(sub1 n)`.
* And when recurring on a list of S-expressions, `l`, use `(car l)` and `(cdr l)` if neither `(null? l)` nor `(atom? (car l))` are true.

It must be changed to be closer to termination. The changing argument must be tested in the termination condition:

* When using `cdr`, test termination with `null?`
* When using `sub1`, test termination with `zero?`

## The Fifth Commandment

When building a value with `+`, always use `0` for the value of the terminating line, for adding 0 does not change the value of an addition.

When building a value with `x`, always use `1` for the value of the terminating line, for multiplying by 1 does not change the value of a multiplication.

When building a value with `cons`, always consider `()` for the value of the terminating line.

## The Sixth Commandment

Simplify only after the function is correct.

## The Seventh Commandment

Recur on the subparts that are of the same nature:

* On the sub-lists of a list.
* On the sub-expressions of an arithmetic expression.

## The Eighth Commandment

Use help functions to abstract from representations.

## The Ninth Commandment

Abstract common patterns with a new function.

## The Tenth Commandment

Build functions to collect more than one value at a time.

# The Five Rules

## The Law of Car

The primitive `car` is defined only for non-empty lists.

## The Law of Cdr

The primitive `cdr` is defined only for non-empty lists. The cdr of any non-empty list is always another list.

## The Law of Cons

The primitive `cons` takes two arguments. The second argument to cons must be a list. The result is a list.

## The Law of Null?

The primitive `null?` is defined only for lists.

## The Law of Eq?

The primitive `eq?` takes two arguments. Each must be a non-numeric atom.
