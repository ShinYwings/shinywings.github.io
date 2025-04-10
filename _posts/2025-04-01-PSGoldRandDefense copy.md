---
title: 백준 골랜디 사용법

categories:
- Others
tags:
- PS
  
comments: true
toc: true

---

참조 사이트: [https://lazypazy.tistory.com/212](https://lazypazy.tistory.com/212)

크롬 확장 프로그램이 사라져서 [solve.ac](https://solved.ac/) 사용해야 한다.

메인 화면 중앙 상단에 검색창에 아래와 같이 필터 기능을 이용한다.

아래는 내가 사용하는 필터이다.  
Alt + Enter 를 입력하면 랜덤으로 한문제가 뽑히고, *s3..g1 는 실버3 ~ 골드1 난이도,  !@$me 는 내가 풀지 않은 문제, 그리고 #s10000.. 는 10000명 이상 푼 문제를 셀렉해준다. 
![example](/assets/img/PSGoldRandDefense/example.jpg)


다른 예시들은 다음과 같다.
```
예시 1. 내가 풀지 않은 골드  
*g !@$me  

예시 2. 내가 풀지 않은 실버1 ~ 실버2
*s1..s2 !@$me 

예시 3. 1000명 이상 푼 dp 문제
s#1000.. #dp
```

### 고급 필터
- %, lang (ex. %ko): 지문이 한국어인 문제들
- t@, tried_by (ex. t@$me): 내가 시도한 문제들
- *, tier (ex: *b..s): 난이도가 브론즈 이상 실버 이하인 문제들 
- s#, solved (ex. s#1000..): 1,000명 이상이 푼 문제들
