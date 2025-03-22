---
title: "BAEKJOON: 입출력과 사칙연산"
date: 2021-05-10 12:00:00 -0500
categories: [Algorithms]
tags: [BAEKJOON]
---

## 2557: Hello World

**문제:**<br/>
Hello World!를 출력하시오.

**입력:**<br/>
없음

**출력:**<br/>
Hello World!를 출력하시오.

```go
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}
```

## 10718: We love kriii

**문제:**<br/>
ACM-ICPC 인터넷 예선, Regional, 그리고 World Finals까지 이미 2회씩 진출해버린 kriii는 미련을 버리지 못하고 왠지 모르게 올해에도 파주 World Finals 준비 캠프에 참여했다.

대회를 뜰 줄 모르는 지박령 kriii를 위해서 격려의 문구를 출력해주자.

**입력:**<br/>
본 문제는 입력이 없다.

**출력:**<br/>
두 줄에 걸쳐 "강한친구 대한육군"을 한 줄에 한 번씩 출력한다.

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("강한친구 대한육군")
	fmt.Println("강한친구 대한육군")
}
```

## 10171: 고양이

**문제:**<br/>
아래 예제와 같이 고양이를 출력하시오.

**입력:**<br/>
없음.

**출력:**<br/>
고양이를 출력한다.

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("\\    /\\")
	fmt.Println(" )  ( ')")
	fmt.Println("(  /  )")
	fmt.Println(" \\(__)|")
}
```

## 10172: 개

**문제:**<br/>
아래 예제와 같이 개를 출력하시오.

**입력:**<br/>
없음.

**출력:**<br/>
개를 출력한다.

```go
package main

import (
	"fmt"
)

func main() {
	fmt.Println("|\\_/|")
	fmt.Println("|q p|   /}")
	fmt.Println("( 0 )\"\"\"\\")
	fmt.Println("|\"^\"`    |")
	fmt.Println("||_/=\\\\__|")
}
```

## 10430: 나머지

**문제:**<br/>
(A+B)%C는 ((A%C) + (B%C))%C 와 같을까?
(A×B)%C는 ((A%C) × (B%C))%C 와 같을까?
세 수 A, B, C가 주어졌을 때, 위의 네 가지 값을 구하는 프로그램을 작성하시오.

**입력:**<br/>
첫째 줄에 A, B, C가 순서대로 주어진다. (2 ≤ A, B, C ≤ 10000)

**출력:**<br/>
첫째 줄에 (A+B)%C, 둘째 줄에 ((A%C) + (B%C))%C, 셋째 줄에 (A×B)%C, 넷째 줄에 ((A%C) × (B%C))%C를 출력한다.

```go
package main

import (
	"fmt"
)

func main() {
	var a, b, c int
	fmt.Scanf("%d %d %d", &a, &b, &c)
	fmt.Println((a+b)%c)
	fmt.Println(((a%c)+(b%c))%c)
	fmt.Println((a*b)%c)
	fmt.Println(((a%c)*(b%c))%c)
}
```

비슷한 문제

- 1000: A+B
- 1001: A-B
- 10998: AxB
- 1008: A/B
- 10869: 사칙연산
- 2588: 곱셈
