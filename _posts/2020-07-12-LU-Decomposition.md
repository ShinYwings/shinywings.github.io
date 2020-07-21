---
layout: post
title: LU Decomposition (LU factorization)
comments: true
categories : [Numerical Analysis]
tags: [LU decomposition]
---

- LU 분해는 데이터의 크기가 커질수록 불리한 가우시안 소거법의 연산을 해결하기 위해
사용된다.

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
## **Python Code**
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
- 아이디어들
> 1. #### 전진 소거는 $$O(n^3)$$ 이 걸리고, 후진 소거는 $$O(n^2)$$이 걸린다.
삼각행렬의 $$O(n^2)$$ 시간 복잡도를 활용하고 싶다.
> 2. #### leading variable 이 1이 아니여도 괜찮다.
변형을 안해줘도 선도 변수를 자유 변수처럼 표현할 수 있지 않을까?
> 3. ##### $$A = LU$$  
Matrix A를 Lower trianglular & Upper trianglular 로 분해할 수 있음.  
이는, 선형계를 풀거나 역행렬을 구할 때 사용  

<img src="C:\githubPage\shinywings.github.io\_posts\image\proof_invertible_matrix.jpg" width="200px" height="200px" title="invertible_matrix_proof" alt="proof_1"></img><br/>

![가역행렬 증명](/_assets/invertible_matrix_proof.jpg)

#### -삼각행렬의 장점
> 선도 변수 앞에 있는 요소들을 스케일링할 필요 없다.
back-substitution의 $$O(n^2)$$ 복잡도를 $$O(n)$$ 으로 줄일 수 있다.


<!-- 알고리즘 렌더링-->
<script>
    pseudocode.renderElement(document.getElementById("Gaussian_Elimination"));
</script>