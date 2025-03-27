---
title: LU Decomposition (LU factorization)
categories:
- Linear Algebra
tags:
- LU decomposition

comments: true
toc: true

pseudocode:
- "Gaussian_Elimination"
- "LU_Factorization"
- "LDU"
---

- 행렬 분해는 가우스 소거법에 비해 컴퓨팅 비용을 많이 줄였다.
![CM_tradoff](/assets/img/LU_Decomp/matrix_time_tradeoff.jpg)
- LU 분해는 첫번째로 **계산의 편리함**, 두번째로 **분석적 용이성**을 위함이 있다. (모든 행렬 분해에 해당)
- ~~**가우시안 소거법**의 시간 복잡도 문제를 해결했다.~~  
**LU분해는 가우스 소거법과 비교하여 <U>flop(floating point operation) 비용의 이점을 갖지 않는다.</U>**    (그럼 왜 계산 비용이 줄은거지??? 아무리 찾아봐도 명쾌한 해답이 나와있지 않다...)  
하지만 **다른 장점**을 갖고 있어 더 선호된다.
> 1. 가우스 소거법은 Augmented Matrx $[A\|b]$를 사용하므로, b의 값을 알아야 한다.  
>반면 LU분해는 행렬 A만 사용하므로 그 분해값을 안다면, 오른쪽 항이 아무리 많이 필요하다고 해도 한번에 하나씩 사용해서 해결할 수 있다. (해 구하기 전 단계)
> 2. $Ax=b$의 해를 구하기 위하여 계산하는 LU분해는 만일 필요하다면, 거의 추가 작업 없이 $A^{-1}$의 계산에 사용할 수 있다. (삼각행렬의 계산은 $O(n^2)$ 이므로)
> 3. LDU 분해를 통해, 한개의 행렬에 L,D,U 모두 저장할 수 있다. 따라서 시스템이 해결하는 데 필요한 메모리의 양을 감소시킨다.  
> 4. 만일 행렬 A가 대부분 영으로 구성되어 있는 크기가 큰 행렬이라면, 또는 영 아닌 성분들이 주 대각선 주위의 "띠(band)"에 집중되어 있따면, LU분해의 비용을 절감하는데 사용할 수 있는 기술이 있다.
> (와닿지 않는다. 차차 알아가자)
  
(안톤의 선형대수학 p589 참조)

## 1. Gaussian Elimination

<pre id="Gaussian_Elimination" style="display:hidden;">
    \begin{algorithm}
    \caption{Gaussian Elimination}
    \begin{algorithmic}

    \FUNCTION {Forward-Substitution}{$A, \vec{b}$}
    \STATE Converts a system $A\vec{x} = \vec{b}$ to an upper-triangular system $U\vec{x} = \vec{y}$  
    \STATE Assumes invertible $A \isin \reals^{n \times n}$ and $\vec{b} \isin \reals^{n}$.
    \STATE $U, \vec{y} \leftarrow A, \vec{b}$
        \FOR{$p = 1$ \TO $n$}
            \STATE *Optionally insert pivoting code here*  
            \STATE $s \leftarrow \frac 1 {u_{pp}}$
            \STATE $y_p \leftarrow s \cdot y_p$
            \FOR{$c = p$ \TO $n$}
                \STATE $u_{pc} \leftarrow s \cdot u_{pc}$
            \ENDFOR
            \FOR{$r = p + 1$ \TO $n$}
                \STATE $s \leftarrow {-u_{rp}}$
                \STATE $y_r \leftarrow y_r + s \cdot y_p$
                \FOR{$c=p$ \TO $n$}
                    \STATE ${u_{rc}} \leftarrow {u_{rc}} + s \cdot u_{pc}$
                \ENDFOR
            \ENDFOR
        \ENDFOR
    \RETURN $U, \vec{y}$
    \ENDFUNCTION  
    
    \FUNCTION {Backward-Substitution}{$U, \vec{y}$}
    \STATE Solves upper-triangular systems $U\vec{x} = \vec{y}$  for  $\vec{x}$.
    \STATE $\vec{x} \leftarrow \vec{y}$
        \FOR{$p = n$ \TO $1$}
            \FOR{$r = 1$ \TO $p - 1$}
                \STATE $x_{r} \leftarrow x_{r} - \frac {u_{rp}x_p} {u_{pp}}$
            \ENDFOR
        \ENDFOR
    \RETURN $\vec{x}$
    \ENDFUNCTION
    \end{algorithmic}
    \end{algorithm}
</pre>

> Gaussian Elimination's Total Running Time
#### $$O(n^3)$$ 

#### ***\*Optionally insert pivoting code here\****
> 가우시안 제거 방법은 pivot의 역수로 scaling 하기때문에 
수치적으로 안정적인 방법으로 *큰 pivot* 을 가져가도록 해야한다.

#### **Pivoting 전략**
 대각행렬 안 절대값이 낮은 피벗 원소를 해당 피벗의 열 벡터중 가장 큰 절대값을 가진 원소로 바꾼다.  

> 1. *Partial pivoting*  
    - Only 행교환   
> 2. *Full pivoting*  
    - 행교환 + 열교환  
  
*Full pivoting은 partial pivoting에 비해 더 많은 computational cost가 필요하지만 수치적으로는 더 안정적임*  
(행렬 전체를 한번 훑어 봄, 우선순위 큐 사용. In practice, rarely necessory)

---------------------------
## **Code**
```python
def forward_substitution(A = "matrix",b = "vector"):

    # AM = np.c_[A, b]   Augmented Matrix
    n = len(A[:,0])
    U = np.matrix.copy(A)   # will be upper triagle matrix
    y = np.matrix.copy(b)
    
    for p in range(0,n):
        
        #partial pivoting    
        pivot = p
        for i in range(p+1,n):
            if np.abs(U[pivot,p]) < np.abs(U[i,p]):
                pivot = i
        U[[p,pivot],:] = U[[pivot,p],:]
        y[[p,pivot]] = y[[pivot,p]]
        # y[p], y[pivot] = y[pivot], y[p] # 이거 안먹힘

        s = 1 / U[p,p]

        y[p] = s * y[p]

        for c in range(p, n):         # 피벗 항 scaling
            U[p,c] = s * U[p,c]

        for r in range(p+1, n):       # 행 연산
            s = -U[r,p]
            y[r] = y[r]+s*y[p]

            for c in range(p, n):
                U[r,c] = U[r,c] + s * U[p,c]

    return U, y

def back_substitution(U="matrix", y="vector"):
    
    x = np.matrix.copy(y)
    n = len(U[:,0])

    for p in range(n-1, -1, -1):
        for r in range(0, p):
            x[r] = x[r] - ((U[r,p]*x[p]) / (U[p,p]))
    return x
```
----------------------------------------------------
## 2. LU Decomposition  
![LU_DEF](/assets/img/LU_Decomp/lu_definition.jpg)
- ### 아이디어들
 1. #### 삼각행렬이 아닌 Square Matrix의 전진 대입법은 $O(n^3)$ 이 걸리고, 후진 대입법은 $O(n^2)$이 걸린다.
>삼각행렬의 $O(n^2)$ 시간 복잡도를 활용하고 싶다.
 2. #### leading variable 이 1이 아니여도 괜찮다.
>변형을 안해줘도 선도 변수를 자유 변수처럼 표현할 수 있지 않을까?
 3. ##### $A = LU$  
> - A를 Lower trianglular & Upper trianglular 로 분해할 수 있음.   
> - 삼각행렬의 대각곱이 Det임을 이용하여 L, U의 대각곱으로 A의 행렬식을 구할때도 용이하다.  
> - A를 LU를 분해하는 과정은 $\approx \frac 2 3 n^3$이 걸린다.  
> (가우스 소거법의 전진 대입과 같은 flop (floating point operation) 이 소요)  


<!-- - ### 삼각행렬의 장점
> 선도 변수 앞에 있는 요소들을 스케일링할 필요 없다.
back-substitution의 $O(n^2)$ 복잡도를 $O(n)$ 으로 줄일 수 있다. -->

- ### 짚고 넘어가야 할 문제
 1. 모든 행렬 A는 LU 분해가 가능한가요?
 2. 행렬 A가 LU 분해 가능하면 유일하게 L, U가 정해지나요?
 3. (1)의 답이 **NO** 라면 어떤 행렬이 LU분해가 가능한가요?  
> (1) 아니오  
> (2) 아니오  
> (3)의 경우, 정방행렬 A를 행교환 없이 가우스 소거하여 REF를 만들수 있다면 LU분해가 가능하다.  
> 왜냐하면 상삼각행렬 $U$가 유도되기 때문이다.  
> 그러면  
><img src="/assets/img/LU_Decomp/a.jpg" width="600px" height="400px" title="invertible_matrix_proof" alt="proof_1">
이고,
> EA는 A에 같은 기본 행연산을 한것과 같기 때문에,  
> $E_k, ..., E_2 , E_1$ (하삼각 기본행렬) 들로  
> $E_k...E_2E_1A = U$로 표현할 수 있고,  
>기본행렬 E는 반드시 Invertible Matrix 이다.    
> 그러면, $A = E_1^{-1}E_2^{-1}...E_k^{-1}U$을 만족하고,  
> 가역인 L에 대해서,  $ L * L = L$ 과 $L^{-1} = L$ 이므로,  
> $L = E_1^{-1}E_2^{-1}...E_k^{-1}$ 이다.  

  
- (참고) 기본행렬 표현 (where $ l > k $)  

$E_{i} = I_{n \times n} +c \vec{e_l} \vec{e_k}^{T}$  

$E_{i}^{-1} = I_{n \times n} -c \vec{e_l} \vec{e_k}^{T}$  

> 기본행렬의 inverse는 음수, 대각행렬의 inverse는 역수


<pre id="LU_Factorization" style="display:hidden;">
    \begin{algorithm}
    \caption{LU Factorization}
    \begin{algorithmic}

    \FUNCTION {LU-FACTORIZATION-COMPACT}{$A$}
    \STATE Factors $A \isin \reals^{n \times n}$ to $A = LU$ in compact format.
        \FOR{$p = 1$ \TO $n$}
            \FOR{$r = p + 1$ \TO $n$}
                \STATE $s \leftarrow \frac {-a_{rp}} {a_{pp}}$
                \STATE $a_{rp} \leftarrow -s$ $\qquad\triangleright$ A에다 L 성분 저장, L은 $E^{-1}$ 이므로 음수  
                \FOR{$c=p+1$ \TO $n$}
                    \STATE ${a_{rc}} \leftarrow {a_{rc}} + s \cdot a_{pc}$
                \ENDFOR
            \ENDFOR
        \ENDFOR
    \RETURN $A\qquad\triangleright$LU분해된 A임
    \ENDFUNCTION  
    \end{algorithmic}
    \end{algorithm}
</pre>
- Result  
$$A = \left( \begin{array}{cccc} U & \cdots & \cdots & \cdots & U \\ L & U & U & U & \vdots\\ \vdots & L & \ddots & U & \vdots\\ \vdots & L & L & U & \vdots \\ L & \cdots & \cdots & L & U \end{array} \right)$$  

> LU Factorization-compact's Total Running Time
#### $O(n^3) \approx \frac 2 3 n^3$  


 1. 잃어버린 L 의 대각행렬 성분은 모두 1이다.  
 2. 인수 분해하는데 걸린 전체 시간은 $O(n^3)$ 이지만,  
 $A\vec{x}=\vec{b}$를 푸는데 전진 대입, 후진 대입을 사용하면 오직 $O(n^2)$ 밖에 안걸린다.  

-----------------------
## **Code**
```python
def LU_Factorization_compact(A):

    M = np.copy(A)
    n = len(M[:,0])

    for p in range(0, n):
        for r in range(p+1, n):
            s = (-M[r,p]) / (M[p,p])
            M[r,p] = -s #하삼각행렬 E^-1 에 저장
            for c in range(p+1, n):
                M[r,c] = M[r,c] + s * M[p,c]

    return M
```
----------------------  

#### **L, U는 유일하지 않다. 그래서 유일한 L, U를 추출해주고 싶다.**
> U의 대각행렬 성분을 따로 빼주면 유일하게 인수분해가 될 수 있다.  
 $\triangleright$ LDU 분해 사용

<pre id="LDU" style="display:hidden;">
    \begin{algorithm}
    \caption{LDU Factorization}
    \begin{algorithmic}

    \FUNCTION {LDU-FACTORIZATION-COMPACT}{$A$}
    \STATE $\triangleright$Factors $A \isin \reals^{n \times n}$ to $A = LDU$ in compact format.
        \FOR{$p = 1$ \TO $n$}
            \FOR{$r = p + 1$ \TO $n$}
                \STATE $s \leftarrow \frac {-a_{rp}} {a_{pp}}$
                \STATE $a_{rp} \leftarrow -s$ $\qquad\triangleright$ A에다 L 성분 저장, L은 $E^{-1}$ 이므로 음수  
                \FOR{$c=p+1$ \TO $n$}
                    \STATE ${a_{rc}} \leftarrow {a_{rc}} + s \cdot a_{pc}$
                \ENDFOR
            \ENDFOR
        \ENDFOR
        \STATE $\quad$
        \STATE *추가부분*
        \FOR{$p = 1$ \TO $n$}
            \FOR{$r = p + 1$ \TO $n$} 
                \STATE ${a_{pr}} \leftarrow \frac {a_{pr}} {a_{pp}}\qquad\triangleright$ Diagonal 성분을 따로 빼줌  
            \ENDFOR
        \ENDFOR
        \STATE $\quad$
    \RETURN $A\qquad\triangleright$LDU분해된 A임
    \ENDFUNCTION  
    \end{algorithmic}
    \end{algorithm}
</pre>  

- Result  
$$A = \left( \begin{array}{ccc} D & U & \cdots & \cdots & U \\ L & D & U & U & \vdots\\ \vdots & L & \ddots & U & \vdots\\ \vdots & L & L & D & U \\ L & \cdots & \cdots & L & D \end{array} \right)$$  

-----------------------
## **Code**
```python
def LDU_Factorization_compact(A):

    M = np.copy(A)
    n = len(M[:,0])

    for p in range(0, n):
        for r in range(p+1, n):
            s = (-M[r,p]) / (M[p,p])
            M[r,p] = -s #하삼각행렬 E^-1 에 저장
            for c in range(p+1, n):
                M[r,c] = M[r,c] + s * M[p,c]

    #LDU로 바꿔 유일하게 인수분해 될 수 있게 바꿔줌
    for p in range(0,n):
        for r in range(p+1, n):
            M[p,r] = M[p,r] / M[p,p]

    return M
```
----------------------  

> ### LDU 분해로 구한 Diagonal Matrix 의 모든 성분의 곱은 Det(A) 이다.  

### LU 분해를 Pivoting 하는 방법 : PLU
> LU 앞에 Permutation Matrix 곱해준다. (순서 바꾼 Identity Matrix)  
> $ P == P^{-1} $ 를 이용
