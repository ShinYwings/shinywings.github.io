---
title: Covariance Matrix
comments: true

categories:
- 개념 정리
tags:
- Statistics
- Covariance matrix

comments: true
toc: true

---


## 위키피디아 정의    
     
- 주어진 random vector의 각각 쌍의 원소들 사이의 공분산이 주어진 정방행렬

- random vector: random variable with multiple dimensions     
  (vector는 scalar양을 가짐; 관측된 경험 value (indefinite) 또는 유,무한의 potential value)

- Diangonal은 variance $\sigma^2$이다.

## 정의      

- 두 확률 변수 사이 서로 간의 영향을 얼마나 끼치는지 (correlation) 나타내는 것을 행렬로 표현한 것이다.

- 공분산 행렬    
$$\begin{matrix} 
Cov(X,X) & Cov(X,Y) & Cov(X,Z)\\
Cov(Y,X) & Cov(Y,Y) & Cov(Y,Z)\\
Cov(Z,X) & Cov(Z,Y) & Cov(Z,Z)\\
\end{matrix}$$

확률 변수 X,Y,Z의 상관계수와 공분산이다.     
공분산(e.g. Cov(X,Y))을 해당하는 확률 변수 두개 (e.g. X,Y)의 표준편차로 나누어 표준화한 값은 X와 Y의 상관계수 (coefficient correlation) 이다.     
- Cov(X,Y) == Cov(Y,X) 이므로 공분산 행렬은 대칭행렬이다.
    
correlation $\rho$에 대해    
          
$$\begin{aligned}  \rho = \frac {1} {N} \displaystyle\sum_{i=1}^{N}(\frac {X_i-\mu_1} {\sigma_1})(\frac {Y_i-\mu_2} {\sigma_2})\\
= E\Big[(\frac {X_i-\mu_1} {\sigma_1})(\frac {Y_i-\mu_2} {\sigma_2})\Big]\\=\frac {E[(X_i-\mu_1)(Y_i-\mu_2)]} {\sigma_1\sigma_2}\\= \frac {Cov(X,Y)} {\sigma_1 \sigma_2} \end{aligned}$$    
where,     
      $N$ = 데이터 갯수    
      $\mu_1$ = X의 평균    
      $\mu_2$ = Y의 평균    
      $\sigma_1$ = X의 표준편차    
      $\sigma_2$ = Y의 표준편차    

$$\begin{aligned} Cov(X,Y) = E[(X-\mu_1)(Y-\mu_2)]
\\= E[XY-\mu_2X-\mu_1Y+\mu_1\mu_2]
\\= E[XY]-\mu_2E[X]-\mu_1E[Y]+\mu_1\mu_2
\\= E[XY]-\mu_2\mu_1-\mu_1\mu_2+\mu_1\mu_2
\\= E[XY]-\mu_1\mu_2\end{aligned}$$    
> Expectation Value 구할때 Degree of Freedom 1(계산하는 데 쓰인 독립 변수, 자기 자신) 생각해서 꼭 N-1 해줘야 함

- **상관관계는 인과관계가 절대 아니다**
  - (e.g. 폭력물 시청과 아이의 폭력성)

- **평균과 비율을 기초해서 구한 상관관계는 변수간의 관계를 과장시킨다.**    

