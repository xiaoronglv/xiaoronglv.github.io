---
title: "Golang: why does sort take value as parameter?"
layout: post
guid: DiehfXaCBmxjDVT9S1lhLKBi
date: 2019-04-27 08:28:39
coffee:
tags:
  -
---

When go through [Go by Example](https://gobyexample.com/sorting), I found a very strange case relating to function invocation.


Every time a variable is passed as parameter, a new copy of the variable is created and passed to called function or method. The copy is allocated at a different memory address.This strategy is called **Pass by Value**, which has few side effects.

Therefore, when change struct self, we have to pass the pointer to function. For example, change the age of student:

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



## Counterintuitive example

But this example provided [Go by Example](https://gobyexample.com/sorting) is counterintuitive. `sort.Strings()` takes a value as parameter, and change this value self! How does it happen?

```go
package main
import "fmt"
import "sort"
func main() {

    strs := []string{"c", "a", "b"}
    sort.Strings(strs)
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

From my understanding, the right way to sort this slice should be

```
sort.Strings(&strs)
```


## Answer

The key is slice!

> A slice is a descriptor of an array segment. It consists of a pointer to the array, the length of the segment, and its capacity (the maximum length of the segment).


![](/media/files/2019/2019-04-27-slice-1.png)

![](/media/files/2019/2019-04-27-slice-2.png)


When sort.Strings(sliceA), a brand new slice sliceB will be created, which also points to the array.

![](/media/files/2019/2019-04-27-slice-3.png)

After the array under the hood has been sorted, I could print the sorted array through SliceA.

That's the magic! Slice has a pointer to the real array!

## Reference

1.  [Stack Overflow: Are golang slices pass by value?](https://stackoverflow.com/questions/39993688/are-golang-slices-pass-by-value/39993797#39993797)

2. [Internal of Slice](https://blog.golang.org/go-slices-usage-and-internals)

3. [Stack Overflow: Why does `sort` take value as parameter, rather than pointer?](https://stackoverflow.com/questions/55862278/golang-why-does-sort-take-value-as-parameter-rather-than-pointer)

