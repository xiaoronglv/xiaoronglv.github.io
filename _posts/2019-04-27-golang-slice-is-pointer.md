---
title: "Golang: why does sort take value as parameter, rather than pointer?"

layout: post
guid: DiehfXaCBmxjDVT9S1lhLKBi
date: 2019-04-27 08:28:39
coffee:
tags:
  -
---

For golang, every time a variable is passed as parameter, a new copy of the variable is created and passed to called function or method. The copy is allocated at a different memory address.This strategy is called **Pass by Value**, which has few side effects.

**Therefore, when change struct self, we have to pass the pointer to function.** For example, I would like to change the age of student:

```go
type student struct {
	name string
	age  int
}

func changeAge(s *student) {
	s.age = 10
}

s := student{name: "Ryan Lv", age:  14}

// you need to pass a pointer to this function.
changeAge(&s) 
```

When go through [Go by Example](https://gobyexample.com/sorting), I found a very strange case relating to function invocation.

## Counterintuitive example

```go
package main
import "fmt"
import "sort"
func main() {

    strs := []string{"c", "a", "b"}
    sort.Strings(strs) // attention!
    fmt.Println("Strings:", strs)

    ints := []int{7, 2, 4}
    sort.Ints(ints)
    fmt.Println("Ints:   ", ints)

    s := sort.IntsAreSorted(ints)
    fmt.Println("Sorted: ", s)
}
```


Output

```
$ go run sorting.go
Strings: [a b c]
Ints:    [2 4 7]
Sorted:  true
```


In this example, the counterintuitive fact is `sort.Strings(strs)` took a slice as parameter, which is a value[1], sorted it, and changed it! 

How does it happen?

## Answer

The key is slice!

> A slice is a descriptor of an array segment. It consists of a pointer to the array, the length of the segment, and its capacity (the maximum length of the segment).


![](/media/files/2019/2019-04-27-slice-1.png)

![](/media/files/2019/2019-04-27-slice-2.png)


When sort.Strings(sliceA), a brand new slice sliceB will be created, which also points to the array.

![](/media/files/2019/2019-04-27-slice-3.png)

After the array under the hood has been sorted, I could print the sorted array through SliceA.

That's the magic! Slice has a pointer to the real array!

## Note

1. At the beginning, I though of slice as "value". Actually it's not.

## Reference

1.  [Stack Overflow: Are golang slices pass by value?](https://stackoverflow.com/questions/39993688/are-golang-slices-pass-by-value/39993797#39993797)

2. [Internal of Slice](https://blog.golang.org/go-slices-usage-and-internals)

3. [Stack Overflow: Why does `sort` take value as parameter, rather than pointer?](https://stackoverflow.com/questions/55862278/golang-why-does-sort-take-value-as-parameter-rather-than-pointer)

