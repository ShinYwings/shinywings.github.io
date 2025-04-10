---
title: Analytic Gradient vs Numerical Gradient
categories:
- 개념 정리
tags:
- Deep Learning
- Analytic Gradient
- Numerical Gradient

comments: true
toc: true
---

## In CS231n,
#### Analytic Gradient 
    the analytic gradient is exact, fast to compute but more error-prone since it requires the derivation of the gradient with math.
    
#### Numerical Gradient
    The Numerical gradient is simple but it is approximate and expensive to compute.

Hence, in practice we always use the analytic gradient and then perform a **gradient check**, in which its implementation is compared to the numerical gradient.

그래서 나는 AG나 NG가 mini-batch gradient descent가 full batch gradient descent에 비해 수렴 속도가 늦는다는 통념?에 대한 설명이라 생각한다.

근데, mini-batch gradient descent은 stochastic적으로 full batch에 대한 gradient를 근사 (approximate) 하는 것이다.

이 맥락을 Batch Normalization으로 가져다 놓으면,     

training 할 때 각 mini-batch의 mean과 variance를 구한다.  mean과 variance는 mini-batch의 분포를 (대표, 요약, 설명, 압축)할 수 있다.
> 분포 -> mean, var -> 원래 분포를 표현할 수 있지만  분포 -> median, quantile -> 원래 분포로는 불가능하다     

그래서 각 mini-batch의 mean과 variance를 평균을 내서 저장한다. 그러면 Full-batch에 대한 mean과 variance를 구할 수 있다.

즉, training때는 mini-batch의 mean과 variance를 통해 analytic gradient를 구해 가중치 업데이트하는데 사용한다.
> BN post에 보면 BackPropagation과정에서 dl/dx = 정규화된 x의 미분값 + 평균의 미분값 + 분산의 미분값이다.     

~~반면에, inference할 때, analytic gradient를 사용하면 정확히 테스트를 진행할 수가 없다. 왜냐하면 컴퓨터는 완벽한 gradient를 계산할 수 없고 approximate 이기 때문이다. (in CS231n)~~    
예를 들어, 한번에 미분할 수 있는 것을 100개로 쪼개서 미분을 하게되면 condition error(=forward error - backward error)가 에러가 1번 발생할 것이 100번 발생할 것이기 때문이다. (100배 커지는다고는 확신 못하겠다.)    
> forward error : input을 알면 그 추정치와 true 값과의 차이
> backward error : input값을 모르지만 추정치를 알 수 있을때 true 값과의 차이     

그래서 dropout, bn 등 inference mode에서는 training과 같은 방법을 사용하면 안된다.    
    
~~그렇기 때문에 inference 때는 BN에서 사용할 numerical한 것이 필요하기 때문에 training 과정때 full-batch에 대한 mean과 variance를 계산한 것이다.
그래서 numerical gradient 라고 하고 싶은데 inference때는 gradient update를 안하잖아.... ㅠㅠ~~    
<h4>바보다..................................... cs231n에서 말하는 gradient check은 우리가 손으로 계산한게 analytic gradient고
컴퓨터가 계산하는게 numerical gradient라고 말한건데 내가 보고싶은대로 해석함..............</h4>

그래서 더 찾아보기로 했다.

#### Analytic Solution vs Numerical Solution

In mathematics, some problems can be solved analyticaaly and numerically.

- An analytical solution involves framing the problem in a well-understood form and calculating the exact solution (and fast)
- A numerical solution means making guesses at the solution and **testing whether the problem is solved well enough to stop.**

numerical method is being used due to the limitations of time or hardware capacity(can't fit all the data into the memory to perform the analytical calculation)     
Moreover, analytical solution is unknown, so we have to work with numerical approach.

#### Analytical Solution
Many problems have well-defined solutions that are obvious once the problem has been defined.
A set of logical steps that we can follow to calculate an exact outcome.

#### Numerical Solutions
There are many problems that we are interested in that do not have exact solutions.
Or at least, analytical solutions that we have figured out yet.
**We have to make guesses at solutions** and test them to see how good the solution is. This involves framing the problem and using trial and error across a set of candidate solutions.
