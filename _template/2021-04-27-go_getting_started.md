---
title: "GO Tutorial: Getting started"
date: 2021-04-27 12:00:00 -0500
categories: [Go]
tags: [Go]
---

Go 를 처음 접하는 개발자의 시점에서 Basic syntax를 정리해보려 한다.

## Packages and Imports

- This code groups the imports into a parenthesized, `factored` import statement
- In Go, a name is exported if it begins with a capital letter

  ```go
  package main

  import (
    "fmt"
    "math/rand"
  )

  func main() {
    fmt.Println("My favorite number is", rand.Intn(10))
  }
  ```

## Functions

- A function can take zero or more arguments
- Each argument has a type beside or when more than one consecutive named function parameters share a type, omit the type from all but the declare at the end

  ```go
  func swap(x, y string) (string, string) {
    return y, x
  }
  ```

  - `Multiple results`: a function can return any number of results

  ```go
  func split(sum int) (x, y int) {
    x = sum * 4 / 9
    y = sum - x
    return
  }
  ```

  - `Naked return`: a return statement without arguments returns the named return values
  - `defer`: excute something after finish the function

## Basic Types

| type      | others                             | zero values | note            |
| --------- | ---------------------------------- | :---------: | --------------- |
| bool      |                                    |    false    |                 |
| string    |                                    |     ""      |                 |
| int       | int8 int16 int32 int64             |      0      |                 |
| unint     | uint8 uint16 uint32 uint64 uintptr |      0      |                 |
| byte      |                                    |             | alias for uint8 |
| rune      |                                    |             | alias for int32 |
| float32   | float64                            |      0      |                 |
| complex64 | complex128                         |             |                 |

## Variables and Constant

- The var statement declares a list of variables

  ```go
  var version float64 = 16.3
  // OR
  vers := 16.3 // inference type: float64
  const language string = "Golang"
  ```

  - **Short variable declarations**(`:=`): short assignment statement can be used in place of a `var` declaration with implicit type inside a function. Short variable declarations does not support outside of a function
  - **Constants** can be character, string, boolean, or numeric values
  - Constants cannot be declared using the := syntax.

## for, range, ...args

- The init and post statements are optional
- At that point you can drop the semicolons: C's while is spelled for in Go

  ```go
  for i := 0; i < 10; i++ {
    sum += i
  }
  ```

  - If you omit the loop condition it loops forever, so an infinite loop is compactly expressed

  ```go
  for index, number := range numbers {
    fmt.Println(index, number)
  }
  ```

  - For looping through an array, `range` can be used. first return value is index, and second return value is value in the array\[index\]

## if Statement

- Like `for`, the `if` statement can start with a short statement to execute before the condition
- Variables declared inside an `if` short statement are also available inside any of the `else` blocks

  ```go
  if v := math.Pow(x, n); v < lim {
  	return v
  } else {
  	fmt.Printf("%g >= %g\n", v, lim)
  }
  ```

## Switch Statement

- Go's switch is like the one in C, C++, Java, JavaScript, and PHP, except that Go only runs the selected case, not all the cases that follow.

  ```go
  today := time.Now().Weekday()
  switch time.Saturday {
  case today + 0:
    fmt.Println("Today.")
  case today + 1:
    fmt.Println("Tomorrow.")
  case today + 2:
    fmt.Println("In two days.")
  default:
    fmt.Println("Too far away.")
  }
  ```

## Defer

- A defer statement defers the execution of a function until the surrounding function returns.
- Deferred function calls are pushed onto a stack. When a function returns, its deferred calls are executed in last-in-first-out order.

  ```go
  func main() {
    fmt.Println("counting")

    for i := 0; i < 10; i++ {
      defer fmt.Println(i)
    }

    fmt.Println("done")
  }
  ```

## Reference

- [A Tour of Go](https://tour.golang.org/list)
- [Go Introduction](https://www.youtube.com/watch?v=jIwtEXBmt38)
