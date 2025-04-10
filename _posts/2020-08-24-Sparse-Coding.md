---
title: Sparse Coding
categories:
- 개념 정리
tags:
- Computer Vision
- Sparse Coding
- Denoising
  
comments: true
toc: true
---

- unsupervised learning 방법
- overcomplete basis vector를 기반으로 데이터를 좀 더 효율적으로 표현하기 위한 용도로 개발이 됨    
- primary visual cortex의 동작을 sparse code를 통해 체계적으로 설명할 수 있음을 보임    
    "Emergence of simple cell receptive field properties by learning sparse code for natural iamges", Olshausen, 1996    
    "Sparse coding with an overcomplete set: a strategy employed by V1", Field, 1997

![Sparse coding](/assets/img/SparseCoding/sparse_coding.jpg)  
Dictionary D는 column 방향으로 atom 이라고 부르는 basis vector를 갖고 있으며, 데이터 X를 $\alpha$로 나타낸다.
여기서 $\alpha$ 안의 빨간색에 해당하는 부분은 0이 아니기 때문에 결과적으로는 해당 위치에 기저벡터를 $\alpha$의 각 원소만큼 곱해서 더하는 방식으로 표현이 된다.

- 여기서 풀어야 할 문제: D와 $\alpha$를 어떻게 구할까?    
    
    $\arg \min_{D,A} ||X-AD||^2_F+\beta\displaystyle\sum_{i,j}|\alpha_{i,j}|$
    
    $\arg \min_{D,A} ||X-AD||^2_F$ : reconstruction error    
        - AD를 이용해 X를 얼마나 잘 표현할 수 있는지를 나타냄     
    $\beta\displaystyle\sum_{i,j}|\alpha_{i,j}|$ : penalty    
        - 기저 벡터 $\alpha$에서 0이 아닌 element의 개수를 제한하는 방향으로 penalty 역할을 한다.    

![Sparse coding2](/assets/img/SparseCoding/sparse_coding_2.jpg)    

- 결과적으로, 위 식을 최소화하는 것이 목표이다. 기본적으로 Matching pursuit(MP)와 Basic pursuit(BP) 방식으로 풀어낸다.
- data compression이나 잡음 제거 및 컬러 interpolation 등에서 효과가 좋다.

- 예시    
    - Denoising    
        ![Sparse coding_denoising](/assets/img/SparseCoding/denoising.jpg)
    - inpainting    
        ![Sparse coding_inpainting](/assets/img/SparseCoding/inpainting.jpg)    

- Sparse Coding 강의 노트
    https://www.cs.ubc.ca/~schmidtm/MLRG/sparseCoding.pdf
    https://www.youtube.com/watch?v=7a0_iEruGoM&feature=emb_logo
    
    

##### Reference
http://blog.naver.com/laonple/220914873095
