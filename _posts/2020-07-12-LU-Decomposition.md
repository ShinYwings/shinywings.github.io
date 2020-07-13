---
layout: post
title: LU Decomposition (LU factorization)
comments: true
categories : [Numerical Analysis]
tags: [LU decomposition]
---

1. 가우시안 소거법을 통한 역행렬 계산

<pre id="Gaussian_Elimination" style="display:hidden;">
 
    \begin{algorithm}
    \caption{Gaussian Elimination for Matrix Inversion}
    \begin{algorithmic}

    \INPUT $n \isin \N$, $Matrix$  $M_{ij}$
    \PROCEDURE{Forward-Substitution}{$n$}
        \FOR{$i = 1$ \TO $n - 1$}
            \STATE Scale row to get pivot $1$
            \FOR{$j = i + 1$ \TO $n$}
                \STATE subtract multiple of row $\vec{i}$ from row $\vec{j}$ to zero out pivot column
            \ENDFOR
        \ENDFOR
    \ENDPROCEDURE
    \PROCEDURE{Backward-Substitution}{$n$}
        \FOR{$i = n$ \TO $2$}  
            \FOR{$j = i - 1$ \TO $1$}
                \STATE zero out rest of column
            \ENDFOR
        \ENDFOR
    \ENDPROCEDURE
    \end{algorithmic}
    \end{algorithm}
</pre>

> Gaussian Elimination's Total Running Time
#### $$O(n^3)$$ 

#### $$A = LU$$  
선형계를 풀거나 역행렬을 구할 때 사용

<!-- 알고리즘 렌더링-->
<script>
    pseudocode.renderElement(document.getElementById("Gaussian_Elimination"));
</script>