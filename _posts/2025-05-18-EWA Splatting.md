---
title: "[TVCG 02] EWA Splatting (Gaussian Splatting 원리 이해를 중심으로)"

categories:
- 논문 리뷰
tags:
- Gaussian Splatting
- EWA Splatting

comments: true
toc: true

---
## 1. Introduction

3D Gaussian Splatting (3DGS)를 깊게 이해하기 위해, [EWA Splatting](</assets/img/EWASplatting/EWASplatting-TVCG02.pdf>) 논문을 읽고 수식 유도 중심으로 제가 이해한 내용을 바탕으로 정리했습니다. 틀린 부분이 있으면 지적바랍니다.
> 전체 과정을 정확히 이해하려면 [Fundamentals of Texture Mapping and Image Warping](</assets/img/EWASplatting/Fundamentals of Texture Mapping and Image Warping.pdf>)을 읽어보는 것을 추천합니다.

본 포스트를 이해하기 위해 다음 사전 지식이 필요합니다:
1. 신호 처리 (Signal Processing)
2. 투영 매핑 (Projective Mapping)
3. 볼륨 렌더링 (Volume Rendering)
4. 텍스처 매핑 (Texture Mapping)

## 2. Gaussian Splatting의 원리  

Gaussian Splatting의 기반이 되는 EWA Splatting은 3D 모델을 복원하기 위한 신호 처리 기법 중 하나입니다. 여기서 EWA는 Elliptical Weighted Average의 약자로, **Elliptical**은 Gaussian 분포의 모양을, **Weighted Average**는 컨벌루션을 통해 Gaussian 분포의 평균을 계산하는 과정을 의미합니다.  
즉, 여러 Gaussian 분포의 평균을 계산하는 것이 EWA Splatting의 핵심입니다.  

이를 쉽게 이해하기 위해, Sample points로부터 원본 이미지를 복원하는 **이상적인** 방법을 예로 들어보겠습니다.  

![](/assets/img/EWASplatting/figure1.jpg){: width="80%" height="80%"}{: .right }*Figure 1. 출처: Udacity*  
  
### (a) Continuous Input Signal
: Sample points로부터 복원할 타겟 원본 이미지를 보여줍니다.   
![](/assets/img/EWASplatting/figure3.jpg)  

위 그림에서 Image Column은 이미지 Space Domain의 연속 신호로 나타낸 예시를 보여줍니다. 3차원 Plot에서 x축은 x좌표, y축은 y좌표, z축은 intensity를 나타냅니다. 실제로는 이산 신호로 처리되지만, 이론적으로는 연속 신호로 간주하여 분석할 수 있습니다.  
이러한 Space Domain의 연속 신호는 Fourier Transform을 통해 Frequency Domain의 연속 신호로도 나타낼 수 있습니다.

### (b) Point Sampling

: (a)의 원본 이미지를 복원하기 위해 사용할 Sample points를 설정하는 작업입니다.  
![](/assets/img/EWASplatting/figure4.jpg)  

원본 이미지의 주파수 대역을 고려하지 않고 Sampling points를 생성하면 **Aliasing** 문제가 발생할 수 있습니다.  

#### Aliasing 문제  
아래는  원본 이미지를 일정 간격 N으로 샘플링한 결과(Sampled by N)를 통해 발생하는 Aliasing 문제를 보여주는 예시입니다.  
  
![](/assets/img/EWASplatting/figure2.jpg){: width="80%" height="80%"}{: right}*Figure 2. 출처: https://canvas4sh.tistory.com/324*  
  
**체크무늬 식탁보** 또는 **청바지의 주름**같이 (빨간 원) 텍스처가 복잡한 영역에는 **Sampled by 4** 만 되어도 Aliasing이 크게 발생하여 원래 형태를 알아보기 힘듭니다. 이미지에서 이런 영역의 픽셀들을 **High Frequency** 를 가진다고 합니다. 이와 반대로, **식탁 다리** 또는 **마루 바닥** 같은 영역 (파란 원)은 Texture가 간단하기 때문에 **Sampled by 16**이 되어도 원본 형태를 어느 정도 알아볼 수 있습니다. 이러한 영역의 픽셀들은 **Low Frequency**를 가진다고 합니다.

이러한 Aliasing 문제를 해결하기 위해서는 두 가지 전략을 사용해야 합니다.
  
1. Nyquist 이론에 기반한 Sampling  
    **체크무늬 식탁보** 또는 **청바지의 주름** 을 표현할 수 있을 만큼 충분히 많은 Sample Points을 생성해야합니다. 이런 성질을 고려한 주파수를 샘플링 이론에서 Nyquist 주파수라고 합니다. 만약 fig 1.(b)와 같이 일정 간격으로 Grid 형태의 Sample point를 만드는 경우, High Frequency 성분에 맞춰 샘플링 주파수를 조정해야 합니다. 

    > **Nyquist 이론**  
    > 아날로그 신호를 디지털로 변환할 때, 신호에 포함된 가장 높은 주파수 성분($f_{max}$)의 두 배 이상의 빈도로 일정 간격으로 샘플링해야 원래 신호에 가깝게 복원할 수 있습니다. 따라서, 샘플링 주파수는 신호의  대역폭($\omega$)의 두 배 이상이어야 합니다.  
    > EWA Splatting에서 Nyquist 이론을 언급하지만, Aliasing 문제를 제거하는 Prefiltering 사용을 위한 이론적 근거로만 사용됩니다.  

2. Prefiltering  
    복원된 이미지의 Aliasing 또한 High frequency 성분을 가집니다. 그렇기 때문에, Sample point로부터 원래 신호를 복원할 때 Prefiltering을 사용하여 복원 가능한 대역을 제한 (Band limit) 합니다. 그러면 고주파 성분이 복원되지 않기 때문에 Aliasing을 줄이는 데 중요한 역할을 합니다. Band limit을 위해 $sinc(x)$ 또는 Gaussian distribution 같은 Low-pass filter를 주로 사용합니다.  

### (c) Intensity Reconstruction
: 원본 이미지에 맞춰 (b)로부터 샘플된 포인트의 intensity (신호에서 Amplitude) 를 조절합니다.  
![](/assets/img/EWASplatting/figure5.jpg)  

복원을 위한 최소한의 Clue가 필요합니다. 그렇기 때문에, Sample point의 intensity는 원본 이미지의 intensity와 같아야 합니다.  

### (d) Reconstruction
: 임의의 Reconstuction Filter와 convolution을 통해 원본 이미지를 복원합니다.  
![](/assets/img/EWASplatting/figure6.jpg)  

$sinc(x)$는 Box filter로써, 일정 대역폭 내 모든 신호를 받는 함수입니다. Sample point가 완벽하다는 가정하에 가장 이상적인 필터입니다.  

### (e) Result
: (d)로부터의 결과 이미지를 보여줍니다. (a)와 비교했을 때, Aliasing이 발생해 완벽하게 복원될 수 있지 않음을 보여줍니다.   
![](/assets/img/EWASplatting/figure7.jpg)  


> **Gaussian Splatting에서는 어떻게 사용할까?**  
> **(b), (c):** 이미지의 Feature points를 입력으로 Adaptive Density Control을 통해 **Importance Sampling** 합니다. Feature point는 High Frequency 성분을 가진 이미지의 중요한 부분을 의미합니다. Feature point 관련 자세한 사항은 SIFT 논문을 참고바랍니다.  
> c.f. Importance sampling을 사용하는 경우 사실 신호 처리 기반 이론이 적용할 수 없습니다. [이 블로그](https://leeyngdo.github.io/blog/computer-graphics/2024-04-09-gaussian-splatting/)에서 non-uniform sampling일 경우를 참조바랍니다. 
> 
> **(d):** Reconstruction filter로 Gaussian distribution을 사용합니다.

## 3. EWA Splatting

Section 2의 전체 과정을 수식으로 나타내면 다음과 같습니다.  
$$f_{c}(u)=\sum_{k\in N} w_{k} r_{k} (u)$$  
여기서, 타겟 소스 $f_{c}(u)$을 여러 개의 Sample point $w_{k}$와 Reconstruction filter $r_{k}(x)$의 곱으로 만들 수 있음을 나타냅니다.  
만약 $f_{c}(u)$을 3D 모델 (또는 2D 텍스처)일 때, 해당 수식을 다음과 같이 렌더링 파이프라인에 적용하는 것이 EWA Splatting의 핵심입니다.  
c.f., 2D 텍스처 경우 Object space위에 $f_{c}(u)$인 uv 텍스처로부터 만들어진 surfel이 올라갑니다. 그렇기 때문에, 2D 텍스처일 경우 viewing transformation 이전에 texture-to-object 맵핑 함수를 추가해야 합니다.
  
![](/assets/img/EWASplatting/figure8.jpg){: width="60%" height="60%"}{: .right }   
  
### 3.1. Base Process

위 파이프라인을 가능하게 만들기 위한 EWA Splatting의 전체 흐름을 보여줍니다.
Object Space 내 $f_{c}(u)$를 Screen Space 내 $g(x)$로 변환하기 위해 크게 두 가지 feature을 고려합니다.
1. **Warping:** 맵핑 모델 $P(x)$ 설계
2. **Prefiltering:** $h(x)$ 설계 (Anti-aliasing)  

이에 따른, Base process의 전체 과정은 다음과 같습니다. 각 그래프를 잇는 화살표 "warp"와 "filter"가 위에서 언급한 두 가지 feature 입니다.  
  
![](/assets/img/EWASplatting/figure9.jpg)  

1. Attribute function: 타겟 공간 정의 s.t. Section 2의 (b)~(e) 과정 
2. Projected attribute function: 다른 공간으로 맵핑 
3. Band limited attribute function: Prefiltering 추가
4. Discrete output: 맵핑된 공간에서 sampling  
   
이를 통해, EWA Splatting 알고리즘의 궁극적인 목표는 Band limited attribute function $g'_{c}(\mathbf{x})$ 을 적절하게 설계해서 $f_{c}(u)$을 복원하는 것입니다.
$g'_{c}(\mathbf{x})$ 설계 방법은 복원하고자 하는 목표가 "3D 모델이냐" 또는 "2D 텍스처이냐" 에 따라 Resampling 방법의 차이가 있습니다.  
1. **Volume Resampling**: $f_{c}(u)$가 3D 모델일 때  
2. **Surface Resampling**: $f_{c}(u)$가 2D 텍스처일 때  

![](/assets/img/EWASplatting/figure18.jpg){: width="70%" height="70%"}{: .right }     
> **Gaussian Splatting 과 관련성**  
> 3DGS는 Volume resampling 방법을 기반한 알고리즘입니다.  
> 2DGS는 Surface resampling 방법을 기반한 알고리즘입니다.

최종적으로, $g'_{c}(\mathbf{x})=\sum_{k \in \mathbb{N}} w_{k}\mathbf{\rho}_{k}(x)$ 안에 Reconstruction filter $\mathbf{\rho}_{k}(x)$ 로 Gaussian 분포를 사용하면 EWA Splatting이 완성됩니다.
이 때 사용하는 Gaussian 분포 또한 각 Resampling 방법에 맞게 튜닝이 필요합니다.

지금까지 한 말을 정리하면, EWA Splatting은 복원하고자 하는 object (3D or 2D)에 따라 $\mathbf{\rho}_{k}(x)$ 을 설계하는 방법에 대해 기술되어 있습니다. 그러하여, 먼저 $\mathbf{\rho}_{k}(x)$ 설계를 위한 두 Resampling 방법의 프로세스 대한 전체 과정을 설명하고 (Sec. 3.1, 3.2), Reconstruction filter로써 Gaussian 분포가 각 방법에 맞게 어떻게 튜닝되는지 설명하겠습니다 (Sec. 4).  

### 3.1. Volume Resampling

Volume Resampling을 위해, Splatting 알고리즘을 사용해 Object로부터 3D Ray space로 Warping 합니다. 이후, 2D로 차원을 줄여서 Screen space에 이미지를 생성합니다.  

여기서, 생소한 두 가지 항목에 대한 정의가 필요합니다.
1. Ray space  
2. Splatting 알고리즘

#### 3.1.1. Ray space
> [LearnOpenCV 자료](https://learnopencv.com/3d-gaussian-splatting/)를 참조했습니다.  

EWA Splatting 에서 소개되는 Ray space이라는 하나의 추가 공간(좌표계)을 도입하여 Ray을 좌표축에 평행하게 정렬함으로써 Ray을 따라 적분하기 쉽게 합니다. 따라서 NeRF에서처럼 ray를 따라 point sampling을 할 필요가 없습니다. Ray space에서 Screen space으로의 최종 변환에는 depth, color, shading을 포함한 최종 렌더링 이미지 생성이 포함됩니다.

![](/assets/img/EWASplatting/figure10.jpg)*출처: https://learnopencv.com/3d-gaussian-splatting/*  

위 그림과 같이, Ray space 변환은 Non-linear 입니다. 왜냐하면 카메라 공간의 수평 및 수직 성분을 깊이에 따라 나누기 때문입니다.

위 내용을 좀 더 쉽게 설명하자면, Ray가 직선으로 들어오는 (=핀홀 모델을 따르는) 어떤 이미지 내 한 point는 focal length $f$ (깊이)에 따라 $\tan(\theta)$ 경향으로 Non-linear하게 광축으로부터 점점 벗어나게 됩니다.  
![](/assets/img/EWASplatting/figure11.jpg){: width="70%" height="70%"}{: .right }     
따라서, 아래 이미지와 같이 핀홀 카메라로 건물을 촬영했을 때 같은 폭을 가진 건물이더라도 시야각도에 따라 카메라 중심부로부터 바깥에 촬영된 건물의 폭이 더 길게 나타납니다. 
> 참고로 Ray가 직선으로 들어오는 Projective Mapping을 Rectilinear projection이라고 부릅니다.    
   

![](/assets/img/EWASplatting/figure12.jpg){: width="70%" height="70%"}{: .right }   *출처:https://www.cambridgeincolour.com/tutorials/image-projections.html*   

그렇기 때문에, EWA Splatting에서 Camera Space $\rightarrow$ Ray space 맵핑 함수 $\phi(u)$는 선형 공간에서 비선형적 공간으로 변환이기에 Affine transformation을 직접 사용하기 어렵습니다. 대신에, 비선형 변환이 가능한 Local affine transformation을 사용합니다.

Local affine transformation이란 1차 미분까지 사용한 Taylor expansion을 통해 선형 함수로 비선형 변환을 근사한 함수를 의미합니다.  

- 1차항까지의 Taylor expansion
    $$f(x) \approx f(a) + f'(a)(x-a)$$ 
- Local affine transformation
    $$\phi_{u_{k}}(t)=\mathbf{x}_{k}+J_{k}(t-t_{k}), \space\space \mathbf{x}_{k}=\phi(t_{k})$$

    $u_k$: mean
  
이때, Jabcobian $J_{k}$는

$$$$

$$
\begin{align}
J_{k}=\frac {\partial \phi} {\partial t_{k}}
&=\begin{bmatrix}
t_{k,0}/t_{k,2} \\
t_{k,1}/t_{k,2} \\
||(t_{k,0},t_{k,1},t_{k,2})^{T}|| \\
\end{bmatrix}

\begin{bmatrix}
dt_{k,0} \\
dt_{k,1} \\
dt_{k,2} \\
\end{bmatrix} \notag \\

&=\begin{bmatrix}
1/t_{k,2} & 0 & -t_{k,0}/t^2_{k,2} \\
0 & 1/t_{k,2} & -t_{k,1}/t^2_{k,2} \\
t_{k,0}/l' & t_{k,1}/l' & t_{k,2}/l' \\
\end{bmatrix} \notag \\
\end{align}
$$
  
이고, 여기서 Object 공간 $\{t_{0},t_{1},t_{2}\}$ $\leftrightarrow$ Ray 공간 $\{\mathbf{x},x_{2}\}$ 사이의 맵핑 $\phi$ 은 다음과 같습니다.  
c.f. $\mathbf{x}=\{x_{0},x_{1}\} \in \text{Screen 공간}$
$$
\begin{bmatrix}
x_{0} \\
x_{1} \\
x_{2} \\
\end{bmatrix}
= \phi(t)=
\begin{bmatrix}
t_{0}/t_{2} \\
t_{1}/t_{2} \\
||(t_{0},t_{1},t_{2})^{T}|| \\
\end{bmatrix},
\begin{bmatrix}
t_{0} \\
t_{1} \\
t_{2} \\
\end{bmatrix}
=\phi^{-1}(\mathbf{x})=
\begin{bmatrix}
x_{0}/l \cdot x_{2} \\
x_{1}/l \cdot x_{2} \\
1/l \cdot x_{2} \\
\end{bmatrix}
$$

$$l'=||(t_{k,0},t_{k,1},t_{k,2})^{T}||$$  

Local affine transformation이 가능한 이유는 아래 이미지와 같이 비선형 변환을 국소적(local)으로 봤을 때 선형 변환으로 근사 가능하기 때문에 합니다. 해당 수식의 증명과 Jacobian 행렬의 기하학적 의미는 [이 블로그](https://angeloyeo.github.io/2020/07/24/Jacobian.html)를 참조해주세요.  
![](/assets/img/EWASplatting/figure14.jpg)*출처: https://angeloyeo.github.io/2020/07/24/Jacobian.html*   

#### 3.1.2. Splatting 알고리즘
![](/assets/img/EWASplatting/figure19.jpg)  
Volume Resampling은 3D $\rightleftarrows$ 2D (Object space $\rightleftarrows$ Ray space) 문제이기 때문에 **Splatting** 방법을 사용합니다.
**Splatting** 은 심플화된 Volume Rendering 알고리즘을 의미합니다. (이 분야를 정확히 몰라서 EWA Splatting에서만 이렇게 표현된건지는 잘 모르겠습니다.)  
  
아래 그림을 통해, Volume rendering과 Splatting의 차이점을 확인 할 수 있습니다.
> Volume Rendering 관련 설명은 [[논문 리뷰] NeRFs](/posts/NeRFs/index.html) 를 참조해주세요.  
  
![](/assets/img/EWASplatting/figure13.jpg){: width="80%" height="80%"}{: .right }   *(왼쪽) Volume Rendering, (오른쪽) Splatting*


Splatting 알고리즘을 설명하기에 앞서, NeRFs에서 정의된 Volume Rendering은 2D 이미지 $\rightarrow$ 3D 모델을 만들어내는 방법이고 basis function은 다음과 같습니다.
  
$$I(\mathbf{x})=\sum_{k} c_{\lambda}(\mathbf{x},\epsilon)*f'_{c}(\mathbf{x},\epsilon)*T_{i}$$
   
$$T_{i} = \exp(-\int^{\epsilon}_{\mu=0}f'_{c}(\mathbf{x},\mu)) = \prod^{\epsilon}_{\mu=0}\exp(-f'_{c}(\mathbf{x},\mu))$$
  
각 항의 의미는 다음과 같습니다.  
- $T_i$: 누적 투과율 (Accumulated Transmittance)
- $f'_{c}(\mathbf{x},\epsilon)$: 볼륨 밀도 (Volume Density)
- $c_{\lambda}(\mathbf{x},\epsilon)$: 파장 $\lambda$ 에서의 색상 (Radiance)
- $\mathbf{x}=\{x_{0}, x_{1}\} \in \mathbb{r}^{2} \text{ (Screen space)}$
- $x_{2}$: 광축
- $\epsilon$: Voxel 위치의 $x_{2}$
   
> NeRFs에서 Volume Density는 $1-\exp(-x)$ 꼴로 나타냈지만, EWA Splatting에서 Volume Density는 $\exp(-x)$로 표현했습니다. Volume Density는 0~1 사이의 Opacity (%) 값을 나타내기 때문에 두 표현 모두 자유롭게 사용하는게 아닐까 생각합니다...  
>  c.f. $\exp(-x)$는 빛의 감쇄함수를 의미합니다.

그 다음, Volume rendering 수식으로부터 Splatting 알고리즘을 만들기 위해 아래 요소들을 고려합니다.
  
1. $f'_{c}(\mathbf{x},\epsilon)$ **정의**  
    ![](/assets/img/EWASplatting/figure15.jpg){: width="80%" height="80%"}{: .right }*$\rho$는 viewing transformation 그리고 $\phi$는 projective mapping 입니다.*  

    Volume Rendering은 실제 오브젝트 $f_{c}(\mathbf{x},x_2)$을 만들기 위해, 2D 이미지에 투영된 오브젝트를 "역" 맵핑 (Inverse warping)하여 Volume data $f'_{c}(\mathbf{x},\epsilon)$을 만듭니다.  

    여기서 Volume data를 구성하는 각 Voxel을 Reconstruction filter $r'_{k}$ 이라고 볼 수 있기 때문에,

    $$r'_{k}(\mathbf{x})=r_{k}(\rho^{-1}(\phi^{-1}(\mathbf{x})))$$

    와 같이 구성되며, Volume data $f'_{c}(\mathbf{x})$는

    $$f'_{c}(\mathbf{x})=\sum_{k}w_{k}r'_{k}(\mathbf{x})$$
        
    입니다.

2. $\exp(-f'_{c}(\mathbf{x},\epsilon))$**을 선형 함수로 근사**

    $\exp(-f'_{c}(\mathbf{x},\epsilon))$는 비선형이므로, Section 3.1.1. Ray space와 같은 이유로 Taylor expansion을 이용해 선형 근사해야 합니다. 따라서, $\exp(-x) \approx 1-x$ 이므로  

    $$ \exp(-f'_{c}(\mathbf{x},\epsilon)) \approx 1-f'_{c}(\mathbf{x},\epsilon)$$

    입니다.  
    
최종적으로, 위의 두 요소를 고려한 Splatting 알고리즘의 basis function은
  
$$g_{c}(\mathbf{x}) = \sum_{k} c_{k}(\mathbf{x})*w_{k}*q_{k}(\mathbf{x})*\prod^{\epsilon}_{\mu=0}(1-w_{j}q_{j}(\mathbf{x}))$$
  
입니다. 여기서, $q_{k}(\mathbf{x})$는 Ray 상의 Voxel 위치 $\epsilon$ 에서 컨벌루션된 모든 Overlapped reconstruction filter의 을 의미합니다.
  
$$q_{k}(\mathbf{x})=\int_{\mathbb{R}}r'_{k}(\mathbf{x}, x_{2})dx_{2}$$  
  
> 드디어 Volume Resampling을 설명하기 위한 두 가지 항목 Ray space과 Splatting 알고리즘을 설명을 마쳤습니다...  

#### 3.1.3. Volume Resampling Filter 
![](/assets/img/EWASplatting/figure16.jpg){: width="60%" height="60%"}{: .right }

다시 본론으로 돌아와서, EWA Splatting의 Base Process 안에 Splatting 수식 $g_c(\mathbf{x})$에 Prefiltering $h(\mathbf{x})$을 적용해서 band limited attribution function $g'_c(\mathbf{x})$을 구해봅시다.

$$g'_c(\mathbf{x}) = g_c(\mathbf{x}) \circledast h(\mathbf{x}) = \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$$

여기서  
- Prefiltered projected reconstruction kernel $\mathbf{\rho}_{k}(\mathbf{x})=c_{k}o_{k}(q_{k}\circledast h)(\mathbf{x})$
- Projected reconstruction kernel $\mathbf{\rho}_{k}=c_{k}o_{k}q_{k} \in \text{Ray space}$
- 컬러 $c_{k} \approx c_{k}(\mathbf{x})$
- 누적 투과율 $o_{k} \approx \prod^{\epsilon}_{\mu=0}(1-w_{j}q_{j}(\mathbf{x}))$

$c_{k}$와 누적 투과율 $o_{k}$은 계산 가능하게 만들기 위해 constant value로 가정했습니다. (논문을 보면서 느끼는게 사소하지만 직접 풀기에는 복잡한 부분들은 모두 상수로 바꾸어 버립니다...)  

Section 4에서 위 수식을 토대로 Gaussian 분포를 사용한 $\mathbf{\rho}_{k}$의 설계 방법을 기술합니다.   
  
### 3.2. Surface Resampling
  
또 다시 Volume resampling filter와 같이 EWA Splatting의 Base Process 안에 Splatting 수식 $g_c(\mathbf{x})$에 Prefiltering $h(\mathbf{x})$을 적용해서 band limited attribution function $g'_c(\mathbf{x})$을 구해봅시다.

$$g'_c(\mathbf{x}) = g_c(\mathbf{x}) \circledast h(\mathbf{x}) = \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$$

where  
- Prefiltered projected reconstruction kernel $\mathbf{\rho}_{k}(\mathbf{x})=c_{k}(r'_{k}\circledast h)(\mathbf{x})$
- Projected reconstruction kernel $r'_{k}(\mathbf{x})=r_{k}(m^{-1}(\mathbf{x}))$
- $\mathbf{x}=m(u) \leftrightarrow u=m^{-1}(\mathbf{x})$
- $m$: compound mapping
    
여기서 compound mapping $m(u)$는 Surface sampling에서 Warping을 위한 중요한 요소입니다. 아래 이미지와 같이, 두 개의 sub-process (**2D to 3D parameterization, 3D to 2D projection**)을 합친 2D to 2D 맵핑으로 정의되고 있고 기존 Volume Resampling에 비교해서 다음과 같은 변화가 생겼습니다.
   
1. **2D to 3D parameterization**:   
    Surface Resampling의 Surface는 Surfel을 의미합니다. Surfel은 3D Object의 표면의 접선면을 의미합니다. Texture에서 Surfel로 변환하는 함수가 필요하기 때문에 preprocessing 과정이 추가됩니다.  
   - texture-to-object 맵핑 $\psi$  

2. **3D to 2D projection**:  
    기존 Volume resampling과 비슷하지만 Volume 복원이 목적이 아니기 때문에 Splatting 알고리즘을 적용하지 않습니다.  

   - object-to-camera 맵핑 $\rho$
   - camera-to-screen 맵핑 $\phi$
   
![](/assets/img/EWASplatting/figure20.jpg){: width="60%" height="60%"}{: .right }  
  
각 맵핑 함수에 대한 자세한 내용은 **Section 4.3. Surface Resampling 적용**에서 $r'_{k}$의 설계 방법과 같이 기술합니다.
  
## 4. Gaussian Distribution as a Reconstruction filter  

EWA Splatting의 핵심은 $g'_c(\mathbf{x})$의 Reconstruction filter $g_c(\mathbf{x})$와 Prefiltering filter $h(\mathbf{x})$ 둘 다 gaussian 분포를 사용하는 것입니다.  

$$g'_c(\mathbf{x}) = g_c(\mathbf{x}) \circledast h(\mathbf{x}) = \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$$
  
그러나 $g_c(\mathbf{x})$는 Warping과 이미 거친 결과이고 $h(\mathbf{x})$와 컨벌루션을 통한 PreFiltering도 필요합니다. 따라서, Gaussian 분포를 Reconstruction filter을 사용하기 위한 Gaussian 분포, 선형 변환, 그리고 컨벌루션이 수식으로 어떻게 정의되어 있는지 알아야 합니다. (Section 4.1)  

그래야만 Gaussian 분포를 Volume Rendering과 Surface Rendering에 적용하는 방법을 해당 수식들을 가지고 설명할 수 있습니다. (각 Section 4.2 그리고 Section 4.3에서 볼 수 있습니다.)
참고로, Volume Resampling은 3D Gaussian 그리고 Surface Resampling은 2D Gaussian를 사용합니다.  
  
### 4.1. 수식 정의
1. Elliptical Gaussian Filter (For reconstruction filter)
2. Linear warp (For warping)
3. Convolution (For prefiltering)
  
#### 4.1.1. Elliptical Gaussian Filter
Elliptical Gaussian Filter는 Anisotropic한 multi-variate gaussian 형태로 정의됩니다.
$$G^{N}_{V}(\mathbf{x}-p)= \frac {1} {(2\pi)^{N/2}|V|^{1/2}} \exp({-\frac{1}{2}}(\mathbf{x}-p)^TV^{-1}(\mathbf{x}-p))$$

여기서 
- $V:$ 공분산 행렬
- $|\space|$: 헹렬식 (determinant)
- N: dimension
- p: mean
- $V, x, p \in \mathbb{R}^N$  

이고, $V$가 Elliptical한 형태를 가지기 위해서는 다음 조건을 만족해야합니다.
- 반드시 Positive definite 이여야 합니다. (자세한 설명은 [여기](https://studyingrabbit.tistory.com/6?category=911605)를 참조해주세요.)
   - 대칭행렬
   - $N \times N$꼴의 V를 특이값 분해했을 때 $N$개의 고유 값이 모두 양수
- Anisotropic이여야 합니다.
  - 이유 1. Isotropic은 Elliptical이 아닌 circle입니다. 
  - 이유 2. 3DGS 논문에서 볼 수 있다시피, Isotropic할 경우 output의 디테일이 많이 떨어지게됩니다.
    - Isotropic 성질은 장축, 단축의 scale이 같아야 하지만, Anisotropic은 장축, 단축이 서로 다른 scale을 가질 수 있습니다. (즉, 자유도 상승) 
  
#### 4.1.2. Linear Warp
> [Fundamentals of Texture Mapping and Image Warping](</assets/img/EWASplatting/Fundamentals of Texture Mapping and Image Warping.pdf>)의 Section 3.5.7 Linaer Filter Warps를 참조했습니다.

Elliptical Gaussian Filter의 Linear Warp은 다음과 같이 정의됩니다.
  
$$G^{N}_{V}(\mathbf{\Phi}^{-1}(\mathbf{x})-p) = \frac {1} {|M^{-1}|} G^{N}_{MVM^{T}}(\mathbf{x}-\mathbf{\Phi}(p))$$

여기서
- $\mathbf{\Phi}(u)=\mathbf{x}=Mu+c$
- $\mathbf{\Phi}^{-1}(\mathbf{x})=u=M^{-1}\mathbf{x}+c$
- $M$: 임의의 mapping 행렬
- $p$: 상수

이고, 해당 수식을 유도하기 위해 Impulse response (=Sample point)의 특성을 이해해야 합니다.

만약 어떤 Impulse response $h(x)$의 주파수 도메인이 $H(\omega)$일 때, 주파수 대역이 a배로 넓어졌다면, 그 신호의 amplitude는 반드시 1/a 배로 줄여 filter 영역 크기를 유지해야 합니다.    
  
$$ h(x/a)/a \leftrightarrow H(\omega a) $$
  
![](/assets/img/EWASplatting/figure21.jpg){: width="60%" height="60%"}{: .right }  

  
$a$가 Multi-dimension $A$일 때, 다음과 같이 나타냅니다.  
$$|A^{-1}| h(A^{-1}x) \leftrightarrow H(A^{T}\omega )$$   

   
그리고 위 수식을 Elliptical gaussian filter에 적용하면 linear warp 수식을 유도할 수 있습니다.

$$ 
\begin{align} 
    G^{N}_{V}(\mathbf{x}) &= \frac {1} {(2\pi)^{N/2}|V|^{1/2}} \exp({-\frac{1}{2}}\mathbf{x}^{T}V^{-1}\mathbf{x}) \\
    |M^{-1}|G^{N}_{V}(M^{-1}u) &= \frac {1} {(2\pi)^{N/2}|V|^{1/2}|M|} \exp({-\frac{1}{2}}(M^{-1}u)^TV^{-1}(M^{-1}u)) \\
    |M^{-1}|G^{N}_{V}(M^{-1}u) &= \frac {1} {(2\pi)^{N/2}|V|^{1/2}|M^{T}M|^{1/2}} \exp({-\frac{1}{2}} u^T(M^{-1})^{T}V^{-1}M^{-1}u) \\
    |M^{-1}|G^{N}_{V}(M^{-1}u) &= \frac {1} {(2\pi)^{N/2}|MVM^T|^{1/2}} \exp({-\frac{1}{2}}u^T(MVM^{T})^{-1}u)  \\
    |M^{-1}|G^{N}_{V}(M^{-1}u) &= G^{N}_{MVM^{T}}(u) \\
    G^{N}_{V}(M^{-1}u) &= \frac {1} {|M^{-1}|}G^{N}_{MVM^{T}}(u) \\
    
\end{align}
$$

위 결과에 추가로 constant 항을 추가하면 Linear warp가 완성됩니다.
$$G^{N}_{V}(\mathbf{\Phi}^{-1}(u)-p) = \frac {1} {|M^{-1}|}G^{N}_{MVM^{T}}(u-\mathbf{\Phi}(p))$$

    
#### 4.1.3. Convolution
Gaussian filter의 공간 도메인과 주파수 도메인 사이의 관계를 $g(\mathbf{x}) \leftrightarrow G(\omega)$ 라고 한다면,
  
$$g_{V_{1}}(\mathbf{x}) \circledast g_{V_{2}}(\mathbf{x}) \leftrightarrow G_{V_{2}}(\mathbf{x})G_{V_{2}}(\mathbf{x})$$
  
이고, 

$$
\begin{align} 
G_{V_{2}}(\mathbf{x})G_{V_{2}}(\mathbf{x}) &= \exp(-\frac{1}{2} \omega V_{1} \omega^{T}) \exp(-\frac{1}{2} \omega V_{2} \omega^{T}) \notag \\
&= \exp(-\frac{1}{2} \omega (V_{1} + V_{2}) \omega^{T}) \notag \\
&= G_{V_{1}+V_{2}}(\omega) \notag \\
\end{align}
$$
  
이므로,
  
$$g_{V_{1}}(\mathbf{x}) \circledast g_{V_{2}}(\mathbf{x}) = g_{V_{1}+V_{2}}(\omega)$$
  
입니다.  

### 4.2. Volume Resampling에 적용

다시 band limited attribution function $g'_c(\mathbf{x})$을 불러와봅시다.

$$g'_c(\mathbf{x}) = g_c(\mathbf{x}) \circledast h(\mathbf{x}) = \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$$

  
- Prefiltered projected reconstruction kernel $\mathbf{\rho}_{k}(\mathbf{x})=c_{k}o_{k}(q_{k}\circledast h)(\mathbf{x})$
- Projected reconstruction kernel $\mathbf{\rho}_{k}=c_{k}o_{k}q_{k} \in \mathbb{R}^{2}\text{ (Ray space)}$
- 컬러 $c_{k} \approx c_{k}(\mathbf{x})$ (constant value)
- 누적 투과율 $o_{k} \approx \prod^{\epsilon}_{\mu=0}(1-w_{j}q_{j}(\mathbf{x}))$ (constant value)
  
Volume Resampling 방법에서 사용하는 Prefiltered projected reconstruction kernel $\mathbf{\rho}_{k}(\mathbf{x})=c_{k}o_{k}(q_{k}\circledast h)(\mathbf{x})$ 에서 projected reconstruction kernel $c_{k}o_{k}q_{k}$는 다음과 같이 정의됩니다.  

$$c_{k}o_{k}q_{k}(\mathbf{x})=c_{k}o_{k}\int_{\mathbf{R}}\frac {1} {|M^{-1}|} G^{3}_{M^{Y}V_{k}M}(\mathbf{x}-\mathbf{x}_{k}, x_{2}-x_{k2})dx_{2}$$
  
위 수식을 도식화 하면 다음과 같습니다.  
![](/assets/img/EWASplatting/figure22.jpg)

그 다음, Volume rendering의 Object-to-Ray의 "projected"를 정의하기 위해 surface rendering과 마찬가지로 compound mapping $m(u)=Mu+c$을 정의를 해주겠습니다.
  
$$m(u)=\phi(\rho(u))$$  
$$m^{-1}(\mathbf{x})=\rho^{-1}(\phi^{-1}(\mathbf{x}))$$  
  
$\rho$: Object-to-Camera 공간 맵핑 함수 (Viewing transformation)  
$\phi$: Camera-to-Ray    공간 맵핑 함수 (Projective mapping)  
  
Volume resampling은 $\rho$는 affine transformation을 사용하지만 $\phi$는 ray라는 비선형 공간으로 맵핑이기 때문에 local affine transformation을 사용합니다. (Section 3.1.1. ray space 참조)

그러면 Viewing transformation $\rho(u)=Wu+d=t$ ($W$는 회전, $d$는 이동) 그리고 Local affine transformation $\phi_{u_{k}}(t)$는 다음과 같습니다.
  
$$\phi_{u_{k}}(t)=\mathbf{x}_{k}+J_{t_{k}}(t-t_{k}), \space\space J_{t_{k}}=\frac {\partial \phi} {\partial t} (t_{k}),\space\space \mathbf{x}_{k}=\phi(t_{k})$$
   
, 두 수식을 $m(u)=\phi(\rho(u))$에다가 대입하면
  
$$m(u)=J_{k}Wu+\mathbf{x}+J_{k}(d-t_{k})$$  
  
이 나오고, u에 대한 상수항 $\mathbf{x}+J_{k}(d-t_{k})$을 $p$로 묶어주면,
  
$$m(u)=J_{k}Wu+p=Mu+c$$  
  
가 됩니다. 그러므로, Gaussian 분포의 Linear warp $M$ 은 $J_{k}W$라고 할 수 있습니다.  

그 다음, $q_{k}$의 상수 $c_{k}o_{k}$를 제외한 Projected reconstruction kernel $q_{k}$에 $|M^{-1}|=|W^{-1}J_{k}^{-1}|$을 적용하면 다음과 같습니다.
  
$$q_{k}(\mathbf{x})=\int_{\mathbf{R}}\frac {1} {|W^{-1}J_{k}^{-1}|} G^{3}_{J_{k}W^{Y}V_{k}W^{T}J_{k}^{T}}(\mathbf{x}-\mathbf{x}_{k}, x_{2}-x_{k2})dx_{2}$$
   
여기서 $V'^{3}_{k}=J_{k}W^{Y}V_{k}W^{T}J_{k}^{T}$ 라 하고 적분을 풀어주면
  
$$q_{k}(\mathbf{x})=\frac {1} {|W^{-1}J_{k}^{-1}|} G^{2}_{V'_{k}}(\mathbf{x}-\mathbf{x}_{k})$$
  
라는 결과가 나옵니다. i.e., Ray-to-Screen 공간 mapping (3D $\rightarrow$ 2D)

최종적으로, band limited attribution function $g'_c(\mathbf{x})= \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$ 안에  사용할 EWA volume resampling filter $\rho_{k}(\mathbf{x})$는 다음과 같습니다.
   
$$
\begin{align}
\rho_{k}(\mathbf{x}) &= (p_c \circledast h)(\mathbf{x}) \notag \\
&= c_{k}o_{k} \frac {1} {|W^{-1}J_{k}^{-1}|} (G^{2}_{V'_{k}} \circledast G^{2}_{V_{h}})(\mathbf{x}-\mathbf{x}_{k}) \notag \\
&= c_{k}o_{k} \frac {1} {|W^{-1}J_{k}^{-1}|} (G^{2}_{V'_{k}+V_{h}})(\mathbf{x}-\mathbf{x}_{k}) \notag \\

\end{align}
$$
   
여기서, $q_{k} (\mathbf{x})$는 2D 이므로 prefilter $h(\mathbf{x})$는 2D Gaussian 분포 사용
  
### 4.3. Surface Resampling에 적용

Surface resampling을 위한 band limited attribution function $g'_c(\mathbf{x})$는

$$g'_c(\mathbf{x}) = g_c(\mathbf{x}) \circledast h(\mathbf{x}) = \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$$

  
- Prefiltered projected reconstruction kernel $\mathbf{\rho}_{k}(\mathbf{x})=c_{k}(r'_{k}\circledast h)(\mathbf{x})$
- 컬러 $c_{k} \approx c_{k}(\mathbf{x})$ (constant value)
- Projected reconstruction kernel $r'_{k}=\frac {1} {|M_{k}^{-1}|} G^{2}_{M_{k}V_{k}M_{k}^{T}}(\mathbf{x}-m_{k}(u_{k}))$
- Compound mapping $m_{k}=\phi(\rho(\psi(u)))=\mathbf{x}$
  - texture-to-object 맵핑 $\psi$  
  - object-to-camera 맵핑 $\rho$  
  - camera-to-ray 맵핑 $\phi$
  
우의 Surface Resampling의 Projected reconstruction kernel 그리고 compound mapping는 Volume Resampling과 서로 다른점이 있습니다. 아래에서 하나씩 살펴보겠습니다. 

#### Projected reconstruction kernel

Gaussian filter의 일반 형태에서 두 Resampling의 차이점은 다음과 같습니다.
    
$$G^{N}_{V_{k}}(m_{k}^{-1}(\mathbf{x})-u_{k}) = \frac {1} {|M_{k}^{-1}|} G^{N}_{M_{k}V_{k}M_{k}^{T}}(\mathbf{x}-m_{k}(u_{k}))$$
  
|         Feature         | Volume Resampling  |    Surface Resampling    |
| :---------------------: | :----------------: | :----------------------: |
|    Gaussian 차원 $N$    |         3          |           2             |
| $V_{k}$ | a*| b*|
| Compound mapping $m(u)$ |  $\phi(\rho(u))$   |  $\phi(\rho(\psi(u)))$   |
| 맵핑 행렬 $\|M^{-1}\|$  | $\|W^{-1}J^{-1}\|$ | $\|S^{-1}W^{-1}J^{-1}\|$ |

여기서, 
a*$=\begin{bmatrix}
v_{0,0} & v_{0,1} & v_{0,2}/s^{2} \\
v_{1,0} & v_{1,1} & v_{1,2}/s^{2} \\
v_{2,0}/s^{2} & v_{2,1}/s^{2} & v_{2,2}/s^{2} \\
\end{bmatrix}$
, b*$=\begin{bmatrix}
v_{0,0} & v_{0,1} \\
v_{1,0} & v_{1,1} \\
\end{bmatrix}$ 그리고 $S$ 는 $\psi(u)$안에 있는 맵핑 행렬입니다. 
  
**[Compound mapping]**
  
![](/assets/img/EWASplatting/figure20.jpg){: width="60%" height="60%"}{: .right }  
  
앞서 Section 3.2. Surface resampling에서 언급한 것과 같이 compound mapping $\mathbf{x}=m(u)$은 다음과 같은 세 가지 맵핑으로 구성됩니다. 
1. texture-to-object 맵핑 $\psi$  
2. object-to-camera 맵핑 $\rho$  
3. camera-to-ray 맵핑 $\phi$
  
수식 정의 포함하여 각 맵핑에 대하여 자세하게 살펴보겠습니다.  
   
**1. texture-to-object 맵핑**

![](/assets/img/EWASplatting/figure23.jpg)
local parameterization $\psi$ 는 uv 공간에 정의된 텍셀 위치 $u_{k}=(u_{0},u_{1})^{T}$를 texture-to-tagent plane 변환 행렬$S_{k}$로 오브젝트 공간 내 $Q$로 이동 후, Object 공간의 각 포인트 $P_{k}$만큼 이동해 실제 위치로 이동해줍니다. 이를 수식으로 나타내면
  
$$\hat{u}=\psi_{k}(u)=P_{k}+S_{k}u$$
  
여기서, $\hat{u}$는 3차원 포인트입니다.

> 텍스처에 Surface normal $n_{k}$ 정보가 있다는 가정하에 합니다.
  
**2. object-to-camera 맵핑**  
object coordinate $\hat{u}$로부터 camera coordinate $t$ 변환 함수 $\rho$는 Volume Resampling 설명과 같이 affine transformation 사용합니다.
  
$$t=\rho(\hat{u})=W\hat{u}+d$$
  
**3. camera-to-screen 맵핑**  

camera coordinate $t$로부터 screen coordinate $\mathbf{x}$ 변환 함수 $\phi$ 또한 Volume Resampling 과 같이 local affine transformation를 사용합니다. 
   
$$\phi_{u_{k}}(t)=\mathbf{x}_{k}+J_{k}(t-t_{k}), \space\space \mathbf{x}_{k}=\phi(t_{k})$$

그러나 Surface resampling은 2D-to-2D이기 때문에 세 번째 항 $x_{2}$를 제거해줍니다.
  
$$
\text{Surface Resampling: }
\begin{bmatrix}
x_{0} \\
x_{1} \\
\end{bmatrix}
= \phi(t)=
\begin{bmatrix}
t_{0}/t_{2} \\
t_{1}/t_{2} \\
\end{bmatrix}
$$

$$
\text{c.f., Volume Resampling: }\begin{bmatrix}
x_{0} \\
x_{1} \\
x_{2} \\
\end{bmatrix}
= \phi(t)=
\begin{bmatrix}
t_{0}/t_{2} \\
t_{1}/t_{2} \\
||(t_{0},t_{1},t_{2})^{T}|| \\
\end{bmatrix}
$$
  
그러면, Jabcobian $J_{k}$ 또한 변경됩니다.
  
$$
\text{Surface Resampling: }
J_{k}=\frac {\partial \phi} {\partial t_{k}}
=\begin{bmatrix}
1/t_{k,2} & 0 & -t_{k,0}/t^2_{k,2} \\
0 & 1/t_{k,2} & -t_{k,1}/t^2_{k,2} \\
\end{bmatrix}
$$

$$
\text{c.f., Volume Resampling: }
J_{k}=\frac {\partial \phi} {\partial t_{k}}
=\begin{bmatrix}
1/t_{k,2} & 0 & -t_{k,0}/t^2_{k,2} \\
0 & 1/t_{k,2} & -t_{k,1}/t^2_{k,2} \\
t_{k,0}/l' & t_{k,1}/l' & t_{k,2}/l' \\
\end{bmatrix}
$$
  
최종적으로, band limited attribution function $g'_c(\mathbf{x})= \sum_{k} w_{k}\mathbf{\rho}_{k}(\mathbf{x})$ 안에  사용할 EWA surface resampling filter $\rho_{k}(\mathbf{x})$는 다음과 같습니다.
   
$$
\begin{align}
\rho_{k}(\mathbf{x}) &= c_{k}(r'_{k} \circledast h)(\mathbf{x}) \notag \\
&= c_{k} \frac {1} {|W^{-1}J_{k}^{-1}S^{-1}|} (G^{2}_{V'_{k}} \circledast G^{2}_{V_{h}})(\mathbf{x}-m_{k}(u_{k})) \notag \\
&= c_{k} \frac {1} {|W^{-1}J_{k}^{-1}S^{-1}|} (G^{2}_{V'_{k}+V_{h}})(\mathbf{x}-m_{k}(u_{k})) \notag \\

\end{align}
$$
   
여기서, $q_{k} (\mathbf{x})$는 2D 이므로 prefilter $h(\mathbf{x})$는 2D Gaussian 분포 사용

## 5. 왜곡 문제
  
3DGS를 실제 이미지를 Input으로 Forward mapping 한다고 했을 때, 두 가지 왜곡을 고려해야 합니다.

1. 렌즈 왜곡
2. 투영 왜곡

  
### 5.1. 렌즈 왜곡
EWA Splatting(3DGS) 경우 Pinhole 모델을 기반으로 하기 때문에, 논외입니다. 따라서, 3DGUT(CVPR 25), Fisheye-GS 에서 렌즈 왜곡 문제를 해결했습니다.  

### 5.2. 투영 왜곡  
Rendering pipeline이 forward mapping일 때, 3D-2D Projection을 naive한 Affine transform을 쓰면 다음과 같은 결과를 보입니다.  
![](/assets/img/EWASplatting/figure24.jpg)

그 이유는, 아래 그림과 같이, Image space와 Object space 사이에 어떤 직선을 투영할 때, 각 포인트 간 거리의 스케일이 안맞는 문제가 있기 때문입니다.

![](/assets/img/EWASplatting/figure25.jpg)
  
이 현상을 해결하기 위한 가장 대표적인 방법은 Barycentric coordinate를 사용한 Projection interpolation인데 EWA Splatting에서는 다루지 않습니다.
  
이 문제에 대한 해결 방법은 명확히 나온 내용은 없지만 추측을 해보면 Local affine transformation 이 같은 역할을 하는 것 같습니다. 왜냐하면 이 왜곡을 해결하기 위해 [Fundamentals of Texture Mapping and Image Warping](</assets/img/EWASplatting/Fundamentals of Texture Mapping and Image Warping.pdf>)에서도 Incremental Techiques for Screen Order Scanning 방법을 사용하는데 , 아래 전문의 Highlight 친 부분을 보면 Local affine transformation 과 수식이 비슷합니다. 그래서 명확하진 않지만 local affine transformation도 비슷한 효과를 가져온 것이라 추측하고 있습니다.  
  
![](/assets/img/EWASplatting/figure26.jpg){: width="60%" height="60%"}{: .right }
  

## 6. Closing
3DGS 논문만 보면 그 안의 알고리즘을 겉으로만 아는 것 같아서 EWA Splatting을 보기 시작했는데 수식 유도도 잘 안돼고 헷갈리는 부분이 많아서 정리하는데 시간이 정말 오래 걸렸습니다ㅠㅠ 너무 시간을 오래 쏟은감이 없지않아 있는데 그래도 대충 보고 넘어가자니 찝찝한 부분이 있었는데 이제 좀 편안해졌네요!