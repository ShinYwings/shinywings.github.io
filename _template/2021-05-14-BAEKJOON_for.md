---
title: "BAEKJOON: for 문"
date: 2021-05-14 12:00:00 -0500
categories: [Algorithms]
tags: [BAEKJOON]
---

## 15552: 빠른 A+B

**문제:**<br/>
본격적으로 for문 문제를 풀기 전에 주의해야 할 점이 있다. 입출력 방식이 느리면 여러 줄을 입력받거나 출력할 때 시간초과가 날 수 있다는 점이다.

C++을 사용하고 있고 cin/cout을 사용하고자 한다면, cin.tie(NULL)과 sync_with_stdio(false)를 둘 다 적용해 주고, endl 대신 개행문자(\n)를 쓰자. 단, 이렇게 하면 더 이상 scanf/printf/puts/getchar/putchar 등 C의 입출력 방식을 사용하면 안 된다.

Java를 사용하고 있다면, Scanner와 System.out.println 대신 BufferedReader와 BufferedWriter를 사용할 수 있다. BufferedWriter.flush는 맨 마지막에 한 번만 하면 된다.

Python을 사용하고 있다면, input 대신 sys.stdin.readline을 사용할 수 있다. 단, 이때는 맨 끝의 개행문자까지 같이 입력받기 때문에 문자열을 저장하고 싶을 경우 .rstrip()을 추가로 해 주는 것이 좋다.

또한 입력과 출력 스트림은 별개이므로, 테스트케이스를 전부 입력받아서 저장한 뒤 전부 출력할 필요는 없다. 테스트케이스를 하나 받은 뒤 하나 출력해도 된다.

자세한 설명 및 다른 언어의 경우는 이 글에 설명되어 있다.

이 블로그 글에서 BOJ의 기타 여러 가지 팁을 볼 수 있다.

**입력:**<br/>
첫 줄에 테스트케이스의 개수 T가 주어진다. T는 최대 1,000,000이다. 다음 T줄에는 각각 두 정수 A와 B가 주어진다. A와 B는 1 이상, 1,000 이하이다.

**출력:**<br/>
각 테스트케이스마다 A+B를 한 줄에 하나씩 순서대로 출력한다.

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

var reader *bufio.Reader = bufio.NewReader(os.Stdin)
var writer *bufio.Writer = bufio.NewWriter(os.Stdout)

func main() {
	defer writer.Flush()
	var N int
    fmt.Fscan(reader, &N)
	for i := 0; i < N; i++ {
		var A, B int
        fmt.Fscan(reader, &A, &B)
        fmt.Fprintln(writer, A+B)
	}
}
```

## 2439: 별 찍기 - 2

**문제:**<br/>
첫째 줄에는 별 1개, 둘째 줄에는 별 2개, N번째 줄에는 별 N개를 찍는 문제

하지만, 오른쪽을 기준으로 정렬한 별(예제 참고)을 출력하시오.

**입력:**<br/>
첫째 줄에 N(1 ≤ N ≤ 100)이 주어진다.

**출력:**<br/>
첫째 줄부터 N번째 줄까지 차례대로 별을 출력한다.

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

var reader *bufio.Reader = bufio.NewReader(os.Stdin)
var writer *bufio.Writer = bufio.NewWriter(os.Stdout)

func main() {
	defer writer.Flush()
	var N int
	var star string = "*"
	fmt.Fscan(reader, &N)
	for i := 1; i <= N; i++ {
		for j := N-i; j > 0; j-- {
			fmt.Fprintf(writer, " ")
		}
		fmt.Fprintf(writer, "%v", star)
		star += "*"
		fmt.Fprintf(writer, "\n")
	}
}
```

## 10871: X보다 작은 수

**문제:**<br/>
정수 N개로 이루어진 수열 A와 정수 X가 주어진다. 이때, A에서 X보다 작은 수를 모두 출력하는 프로그램을 작성하시오.

**입력:**<br/>
첫째 줄에 N과 X가 주어진다. (1 ≤ N, X ≤ 10,000)

둘째 줄에 수열 A를 이루는 정수 N개가 주어진다. 주어지는 정수는 모두 1보다 크거나 같고, 10,000보다 작거나 같은 정수이다.

**출력:**<br/>
X보다 작은 수를 입력받은 순서대로 공백으로 구분해 출력한다. X보다 작은 수는 적어도 하나 존재한다.

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

var reader *bufio.Reader = bufio.NewReader(os.Stdin)
var writer *bufio.Writer = bufio.NewWriter(os.Stdout)

func main() {
	defer writer.Flush()
	var N, X int
	fmt.Fscanln(reader, &N, &X)
	var sequence = make([]int, N)
	for i := range sequence {
		fmt.Fscanf(reader, "%d ", &sequence[i])
		if sequence[i] < X {
			fmt.Fprintf(writer, "%d ", sequence[i])
		}
	}
	fmt.Fprint(writer, "\n")
}
```
