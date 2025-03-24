---
title: \[ZeroToHero\] (1) NeRF 리뷰 논문 읽기
categories:
- ZeroToHero
tags:
- NeRF
- 3D Reconstruction

comments: true
toc: true
---
## Overview   

- MileStone
  - 물체 하나 스캐닝하기

- 리뷰할 논문: **Neural Radiance Fields for the Real World: A Survey**

- 논문 선정 이유:
  - 실제 환경에서 이게 잘 작동되나 궁금함
  - 2024년도까지 NeRF 관련 논문들이 리뷰됨

- TODO: 
  - Novelty 될만한 것 찾기
  - 개발 범위 정하기 
---

# 1. 문제 정의

$$ F_\theta : (p,d) \rightarrow (c, \sigma)$$

바라보는 "방향" $d \in \R^2$ 과 "위치" $p \in \R^3$ 이 주어졌을 때, Raycasting된 점의 Color $c \in R^3$ 그리고 volume density $\sigma \in \R$ 을 구하기 위한 Probability Function $F_\theta$ 를 만드는 것이다.

여기서, 입력 값 방향 $d=\{ \phi, \psi \}$ 과 위치 $p=\{x,y,z\}$
는 각각 Azimuth, Elevation 각도 그리고 Cartasian 좌표계를 나타낸다. 그리고 Color $c=\{ r,g,b \}$ 를 나타낸다.  
따라서, input은 5D 그리고 output은 4D 이다.
$$input(d_x,d_y,d_z,p_\phi,p_\psi) \rightarrow F_\theta \rightarrow output(c_r, c_g, c_b, \sigma) $$

<br>

# 2. NeRF 구조


Function $F_\theta$ 는 5가지 프로세스로 구성되어 있다.

(1) Sampling $\rightarrow$ (2) Encoding $\rightarrow$ (3) Radiance Fields Estimation $\rightarrow$ (4) Volume Rendering $\rightarrow$ (5) NeRF-Agnostic Enhancement  

간단하게 각 과정에 대해 알아보자.

*Sampling*

Input 을 생성하기 위한 전처리 과정이다.
어떠한 이미지으로부터 각 픽셀마다 Input Ray를 생성한다고 생각하자.

$$Ray_{pixel(x,y)} \in image $$

만약 이미지 해상도가 640x480 이라면 307,200 개의 Ray를 생성해야한다.  
그러면 307,200 개의 Ray가 훈련을 위해 모두 필요할까? $\rightarrow$ 아니다.
> **Ray 갯수와 훈련 속도 간의 상관 관계**  
> 
> Input이 5개밖에 되지 않기 때문에 Ray 갯수 자체가 훈련 시간에 영향을 주기 보다는 각 Ray가 직선을 표현하는 과정에서 Volume Density 를 생성하는게 무겁기 때문에 Ray 갯수가 학습 속도에 영향을 줄 것으로 생각된다.  
> i.e. Ray라는 직선 하나를 "잘" 표현하기 위해 몇 개의 points가 필요할까?

Scanning에 관심없는 영역 (Occluded 또는 Empty area) 을 피하기 위해, *hierarchical volume sampling*을 사용하여 a subset of rays를 랜덤하게 선택한다.

> **왜 관심없는 영역을 피해야할까?**  
> 이에 대해, 나는 상반된 의견 모두 존재한다.
> - Pros  
> 불필요한 Color 및 Volume의 Loss 값은 Backpropagation 과정에서 Weight Update 에 큰 변경을 줄 것이기 때문에 모델을 혼란주어 학습 성능을 저하시킬 것이다.
> 
> - Cons  
> 관심없는 영역에 대해 학습을 피하는 것은 꼭 좋은 방법은 아니다.  
> Unsupervised learning 또는 Language model에서는 의도적으로
> False negative (틀린 입력은 틀리다) 의 학습 데이터를 넣기도 한다.  
>  e.g., Negative sampling

// TODO: hierarchical volume sampling에 대해 설명

> **IDEA**  
> 만약 한 물체의 3D Scan를 하고 싶다면 Instance Segmentation 으로 ROI를 설정하고 그 안에서 Importance Sampling을 한다면 더 좋은 결과를 얻을 수 있지 않을까?

뒤에서 나오지만, Loss 함수는 Coarse model의 loss 와 Fine model의 loss의 선형 결합으로 이루져있다.
hierarchical volume sampling은 Coarse model에서 사용되는 Sampling이고, Fine model에서는 Coarse model로부터 출력된 분포로부터 Inverse transform sampling이 뒤따른다.  

> 지금은 무슨 말인지 이해안되니 그냥 넘어가자.

*Samples Encoding*  

이전 과정에서 얻은 어느 한 Sampled point (Input 5D)는 Ray를 나타낸다.  
여기서 우리의 목표는 Ray로부터 부딪친 물체의 Color와 Volume Density를 얻는 것이다. 그리고 Ray는 직선, 직선은 여러 점으로 이루어진 집합이다. 

즉 Ray를 다음과 같이 표현했을 때,  
```
      ------------------------------------------>
```
각 `-` 는 어떤 Color와 Volume Density를 가질 지에 대한 확률 (정확히는 likelihood) 을 가지고 있으면 된다.  

그래서 Layer 에 넣기 전에 Input을 다시 한번 `--->` 로 변환해야 한다.

그러나 `--->`를 그대로 학습 모델의 Input으로 넣으면 학습이 제대로 되지 않는다.
> **왜 학습이 제대로 되지 않을까?**  
> Ray `--->`의 각 `-` 를 숫자로 바꾸자. $\rightarrow$ `123>`  
> 목표는 각 `1`, `2`, `3` 의 Color와 Volume Density의 확률을 구하는 것인데, 학습 Layer에 들어가는 순간 `1`, `2`, `3` 순서 정보가 사라진다.   
> i.e. 행렬 곱의 결과 벡터 그리고 그 입력 벡터 간의 상관 관계를 알 수 없다.  
> 그렇기 때문에, Positional Encoding을 사용하게 되는데, Backpropagation 과정에서 Encoding된 값들이 미분의 영향을 받지 않기 때문에, Input의 위치 정보를 그대로 유지할 수 있다.  
> 이러한 이유 때문에, 어떤 모델이던지 Input들 간 서로의 위치가 중요하다면 Positional Encoding은 필수이다.

그렇기 때문에, 해당 **Encoding** 과정이 필요하며, Positional Encoding 이라는 기법을 사용한다. (여기서 사용된 방법은 Vision Transformer에서 ~~아마도 처음~~ 소개되었다.)  

Positional Encoding의 함수는 다음과 같이 정의된다.

$$\gamma_L(u)= \lbrace \sin(2^0\pi u),\cos(2^0\pi u), \dots, \sin(2^{L-1}\pi u),\cos(2^{L-1}\pi u) \rbrace$$ 

// TODO: Positional Encoding된 값이 [-1, 1]인지, 초기 값만 [-1, 1] 인지 확인하기.

한 Ray의 각 Element $u$ 는 [-1, 1] 로 normalization 되어 있다.