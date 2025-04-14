---
title: Positional Encoding

categories:
- 개념 정리
tags:
- Positional Encoding
  
comments: true
toc: true

---
> 이 포스트는 [묵묵히 걸어가기님의 블로그](https://tigris-data-science.tistory.com/entry/%EC%B0%A8%EA%B7%BC%EC%B0%A8%EA%B7%BC-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94-Transformer5-Positional-Encoding) 내용을 기반으로 재구성하였습니다.   
   
   
## Introduction
딥러닝 모델에서, Input을 구성하는 요소들은 Layer를 통과하면서 각 요소 간의 상대 위치 정보를 잃게 됩니다.  
Positional Encoding(PE)은 이러한 위치 정보를 보존하기 위해 사용되는 전처리 기법으로, Input 요소 간의 위치가 중요한 모델에서 효과적으로 활용됩니다.

![](/assets/img/PositionalEncoding/PEexample.jpg)

대표적으로 (1) 언어 모델, (2) 비전 모델, (3) NeRF 기반 모델이 있으며, 이 세 모델은 공통적으로 Positional Encoding을 적용하기 위해 Input에 PE를 추가하는 간단한 방식을 사용합니다.  
다만, Input 자체에 Positional Encoding을 직접 적용할 수 없기 때문에, 먼저 Input의 각 요소를 특정 벡터 형태(i.e. Embedding Vector)로 변환해야 합니다.  

따라서 Positional Encoding을 설명하기에 앞서, Input과 Positional Encoding 사이의 중간 다리 역할을 하는 Embedding Vector에 대해 간략히 소개하겠습니다.

### Embedding Vector
Embedding Vector는 벡터 공간에서 Input을 표현하기 위해 사용됩니다.  
![](/assets/img/PositionalEncoding/Picture1.jpg)*예시: "I am going to post something"을 Embedding Vector로 변환*

벡터 공간에서 표현하는 주요 장점은 각 요소 간의 상관 관계를 파악할 수 있다는 점입니다. 두 벡터가 서로 가까울수록 더 높은 상관 관계를 가짐을 나타냅니다.  
여기서, 언어 모델에서 Input 배열의 각 Index는 Token 이라고 부릅니다.  
![](/assets/img/PositionalEncoding/Picture3.gif)*출처: https://www.blossominkyung.com/deeplearning/transfomer-positional-encoding*  

Embedding Vector를 생성하는 방법은 Input의 종류에 따라 달라집니다.

1. 언어 모델  
    예를 들어, Input으로 "I am going to post something."이 주어지면, "I", "am", "going", "to", "post", "something" 각각을 Word Embedding 모델을 통해 Embedding Vector로 변환합니다.
2. 비전 모델  
   Input으로 주어진 이미지를 patch 단위로 나눈 뒤, 각 patch를 Flattening하여 Embedding Vector로 사용합니다.  
3. NeRF 기반 모델  
   Input으로 주어진 Ray들의 Position $\theta$, View Direction $\phi$를 주어진 Embedding Vector로 변환합니다. 여기서는, Feature Vector라고 부릅니다.   
     
  
## Diving Into Positional Encoding  
Transformer에서 소개된 Absolute Positional Encoding은 LLM 등장 이후 Token("I", "am" 등 요소를 지칭)이 확장되어 요소 간의 상대 위치를 인코딩하는 Relative, Rotary 방식의 Positional Encoding도 등장했습니다.  
그러나 이 포스트에서는 Absolute Positional Encoding 방식만 다루겠습니다.  

### Absolute Positional Encoding
Absolute Positional Encoding의 주요 목적은 각 요소의 Embedding Vector에 고유한 위치 정보를 반영하기 위해 고정 길이의 특정 벡터 값을 더하는 것입니다.  
예를 들어, "I am going to post something"이라는 문장이 4차원 Embedding Vector로 표현된다면, Positional Encoding Vector 역시 동일한 4차원으로 구성되며, 각 요소에 고유한 위치 정보를 나타내는 벡터 값을 추가합니다.  
![](/assets/img/PositionalEncoding/Picture2.jpg)

그러나 Dataset으로부터 나온 많은 Input에 대응하는 고유한 위치 정보를 가진 벡터 값을 갖게 만들기 위해 다음과 같은 조건을 따라야 합니다.  

### 이상적인 Positional Encoding 조건  

1. 각 위치의 PE 값은 Deterministic 하게 결정되며 유일해야 한다.
2. 데이터와 관계없이 각 위치의 PE 값은 동일해야 한다.
3. 데이터의 길이와 상관없이 적용 가능해야 한다.
4. 간격이 동일하다면 위치와 관계없이 거리는 동일해야 한다.  
    $$distance(x, x+h) = distance(y, y+h)$$
5. (Optional) 선형 변환을 통해 서로 다른 PE 위치를 알 수 있어야 한다

첫 번째 조건에서 PE 값이 Deterministic하고 유일해야 하는 이유는 다음과 같습니다.
   - **Deterministic**:  
     동일한 위치에 대해 항상 동일한 PE 값을 생성해야 모델이 위치 정보를 일관되게 학습할 수 있습니다. PE가 비결정적이라면, 동일한 입력 데이터라도 다른 PE 값이 생성되어 모델이 순서를 혼동하거나 잘못된 정보를 학습하게 되어 결과의 안정성이 떨어질 수 있습니다.
   - **유일성**:  
     각 위치의 PE 값이 고유해야 모델이 위치를 명확히 구분할 수 있습니다. 만약 서로 다른 위치가 동일한 PE 값을 가진다면, 모델은 위치 정보를 혼동하게 되어 성능 저하로 이어질 수 있습니다.

나머지 조건들은 이러한 첫 번째 조건을 충족시키기 위한 필수 요건으로, 아래 예시 방법들을 통해 그 중요성을 이해할 수 있습니다.

### Example Methods

#### Simple Count Indexing
Simple Count Indexing은 말 그대로 각 위치의 인덱스를 PE 값으로 사용하는 방법입니다.   

$$Positional Encoding(\alpha_{i} = i)$$   

이 방법은 이상적인 PE 조건을 모두 만족하지만, 입력 값이 길어질수록 Encoding 값이 커져 학습 시 문제가 발생할 수 있습니다.  
예를 들어, 학습 모델 내 Hyperbolic Tangent Activation 함수를 사용할 경우, 결과 값이 [-1, 1] 범위를 벗어나면 Backpropagation 과정에서 Gradient Exploding 문제가 발생할 수 있습니다.  
![](/assets/img/PositionalEncoding/Picture4.jpg)

#### Normalized Count Indexing

Normalized Count Indexing은 값이 커지지 않도록 Simple Count Indexing의 결과를 문장의 길이로 나누어 [0, 1] 범위로 정규화하는 방법입니다.  

$$Positional Encoding(\alpha_{i} = \frac {i} {T})$$   

이 방법은 Simple Count Indexing에서 발생하는 문제를 해결했지만, 이상적인 PE 조건 중 두 번째 "**데이터와 관계없이 각 위치의 PE 값은 동일해야 한다.**" 를 만족하지 못합니다.  
![](/assets/img/PositionalEncoding/Picture5.jpg)

#### Binary Count Indexing

Binary Count Indexing은 Simple Count Indexing의 결과를 이진수로 표현하는 방식입니다.  
![](/assets/img/PositionalEncoding/Picture6.jpg)

Simple Count Indexing과 같이 숫자 커지는 문제가 사라졌지만, 이상적인 PE 조건 중 세 번째 **데이터의 길이와 상관없이 적용 가능해야 한다.** 그리고 네 번째 **간격이 동일하다면 위치와 관계없이 거리는 동일해야 한다.** 조건을 만족하지 못합니다.  
각 조건이 만족하지 못하는 이유는
1. Input의 Index 가 많아질수록, 위 그림과 같이 PE의 차원 수가 이를 모두 담지 못합니다. 이는 세 번째 조건을 만족하지 못합니다.  
2. 예를 들어, 각 Index별 3차원 PE 벡터가 다음과 같이 주어졌을 때,   
        
    $$i=1:(0,0,1), \space i=2:(0,1,0), i=3:(0,1,1)$$   

    $(0,0,1) \leftrightarrow (0,1,0)$과 $(0,1,0) \leftrightarrow (0,1,1)$ 간의 간격은 동일하지만, 각각의 거리는 $\sqrt{2}$와 $1$로 다릅니다.  
    그러하여, 네 번째 조건을 만족하지 못합니다.  

#### Sinusoidal Encoding

그럼에도 불구하고, Binary Count Indexing에서 한가지 장점이 있습니다.  
![](/assets/img/PositionalEncoding/Picture7.jpg)
위 그림의 빨간 화살표를 보면 Binary Count Indexing의 $2^0$ 차원의 주기가 가장 짧고, 차원이 올라갈수록 주기가 점점 길어지는 것을 확인할 수 있습니다.  
이와 같이, 각 차원의 Sequence가 일정 범위를 순환하면서 서로 다른 주기를 가질 수 있으면 보다 적은 차원으로 더 많은 수를 효율적으로 표현할 수 있습니다.  
Sine 함수는 이러한 주기성 뿐만 아니라, [-1, 1] 사이의 정규화된 범위 값을 가져 학습에 용이하다는 장점이 있습니다.  

그래서 Sine함수를 사용한 Positional Encoding를 정의하면 다음과 같습니다.  

$$\sin(x_i \frac {\pi} {2^{D+1}})$$

여기서, $x_i$는 Input의 index 값, $D$는 PE의 Dimension의 index 값 입니다.  

이 정의를 그대로 사용하고 싶지만, 역시나 수정이 필요합니다. 두 가지 문제가 있기 때문입니다:  
1. 주기의 시작과 끝 값이 동일하다.  
2. 학습 과정에서 PE 벡터 간 상대 위치를 파악하기 어렵다.

다행히, 약간의 변형을 통해 이 두 문제를 해결할 수 있고, 이를 해결하면 이상적인 PE 조건을 모두 만족하는 최종적인 PE를 볼 수 있습니다.  
  

##### **문제 (1) 해결**  
  

주기성을 띄는 것은 적은 차원으로 많은 값을 표현할 수 있다는 장점이 있었습니다.  
하지만 서로 다른 주기를 가진 여러 Sine 함수를 사용해 PE 벡터를 만들 때 그것들이 표현할 수 있는 값은 일정한 범위를 가지고 있으며, 그 일정한 값의 범위를 매 주기마다 반복하게 됩니다.  

즉, PE가 표현할 수 있는 범위를 넘어가게 된다면 다시 Index 0의 PE Vector 값을 가져 중복됩니다.   

예시를 통해, 위 언급한 내용을 이해해보겠습니다.   
PE Dimension $D$ = [0, 2] 범위의 PE 함수 $\sin(x_i\frac {\pi} {2^{D+1}})$ 은 아래 그림과 같이 세 개의 주기 함수를 가지게 됩니다.  
![](/assets/img/PositionalEncoding/Picture8.jpg)

그 다음, 세 함수가 만들어내는 3차원 공간의 점들을 나타내면 다음과 같습니다. (Dim 3의 -1 방향의 축이 가시화되지 않은 것 같습니다.)  
![](/assets/img/PositionalEncoding/Picture9.jpg)*출처: https://towardsdatascience.com/master-positional-encoding-part-i-63c05d90a0c3/*

위 그림과 같이, PE Vector는 연속된 점들로만 표현되기 때문에, Input의 Index가 늘어난다면 언젠가는 중복된 PE Vector 값이 나올 수 있다는 것을 확인할 수 있습니다.  

Transformer는 해당 문제를 해결하기 위해, 주기를 늦추어 Sine 함수의 단조 증가하는 영역만 사용합니다. 이에 따라, 수식을 다음과 같이 바꾸었습니다.   

$$\sin(x_i / 10000^{2j/d_{model}})$$   

여기서, $x_i$는 index 값, $j$는 PE Vector의 Dimension, 그리고 $d_{model}$은 Embedding Vector의 Dimension을 의미합니다. (512 사용)   

> 10,000 은 실험을 통해 얻어낸 값으로 추측됩니다.   
> 해당 값은 Sine함수를 단조 증가하는 한 무엇이든 될 수 있습니다.

저 함수의 또 다른 장점은 $j$ 값 변화에 따라서도 Sinusoidal 함수의 파형이 변화합니다.

![](/assets/img/PositionalEncoding/Picture10.jpg)*출처: Forgot it...*

따라서, PE의 차원 Index 뿐만 아니라 Input의 Index 에 따라서도 모든 Sinusoidal 함수가 서로 다른 파형을 가져 중복되는 값을 방지할 수 있습니다.   
    
##### **문제 (2) 해결**  
   
PE가 $(\cos\theta, \sin\theta)$ 형태로 구성되어 있다면, 선형 변환 (회전 행렬)을 통해 Token의 Position 별 상관 관계를 나타낼 수 있습니다. 그러하면, 이상적인 PE 조건 중 다섯 번째 **선형 변환을 통해 서로 다른 PE 위치를 알 수 있어야 한다.** 를 만족하여 모델 학습에 도움된다고 합니다.  

이에 대한 수식 해석은 다음과 같습니다.  

$$PE_{x_i+\Delta step} = PE_{pos} \cdot Translation(\Delta step)$$  

$$\begin{equation} \notag
    \equiv
    \begin{bmatrix}
        \cos(\theta + \phi) \\  
        \sin(\theta + \phi)
    \end{bmatrix}
    =  
    \begin{bmatrix}
        \cos(\phi) & \sin(-\phi) \\  
        \sin(\phi) & \cos(\phi)
    \end{bmatrix}
    \begin{bmatrix}
        \cos(\theta) \\  
        \sin(\theta)
    \end{bmatrix}
    
\end{equation}$$  

  
이를 통해, 최종적으로 Transformer에서 사용하는 이상적인 PE 조건을 모두 만족된 Sinusoidal Encoding이 완성되었습니다!  
 
  $$\sin(x_i / 10000^{2j/d_{model}})$$    
  
  $$\cos(x_i / 10000^{2j/d_{model}})$$    
  
여기서 $x_i$가 짝수일 때, sine 함수 사용하고 $x_i$가 홀수일 때, cos 함수 사용합니다.  

이를 가시화하면 다음과 같습니다. X축은 PE Vector Dimension 그리고 Y축은 Input의 길이입니다. (해당 이미지를 -90도로 회전하면 위 설명에서 사용한 표들과 같습니다.)  
![](/assets/img/PositionalEncoding/result.jpg)
  
지금까지 한 결과에 대해, 일반화하면 다음과 같습니다.

$$\gamma(x_i) = \lbrace \sin(\omega x_i), \cos(\omega x_i), \cdots \rbrace$$  

여기서 $\omega$ 는 넓은 의미에서 주파수를 나타내며, 사용 목적에 따라 적절히 설정됩니다. 또한 Sine과 Cosine 함수를 활용하기 때문에 이를 Fourier Feature Mapping 방법이라고 부릅니다.  

### 이미지 처리에서의 PE
[Fourier Features Let Networks Learn High Frequency Functions in Low Dimensional Domains](https://arxiv.org/pdf/2006.10739)에 따르면, 이미지 생성 모델에서 Fourier Feature Mapping 방법을 통해 픽셀의 좌표 값을 위치 인코딩(PE) 한다면, 이미지 내 고주파 성분을 효과적으로 복원할 수 있습니다. 아래 동영상에서는 PE 사용하지 않았을 경우(왼쪽)와 PE 사용했을 경우(가운데)의 훈련 결과(오른쪽)를 비교해 볼 수 있습니다.
{% 
    include embed/video.html 
    src='/assets/img/PositionalEncoding/fourierfeature.mp4' 
    types='ogg|mov'
    title='출처: https://www.matthewtancik.com/nerf'
    autoplay=true
    loop=true
    muted=true
%}

## Closing

### 왜 학습할 때 PE 정보가 계속 유효한가?
PE 기법이 Weight initialization에 영향을 미치는 것으로 보입니다. 그러나 모델 학습에 효과적인 이유에 대한 명확한 근거는 아직 찾지 못했습니다.  
다만, 조사한 자료를 바탕으로 두 가지 가능성을 추측해볼 수 있었습니다:
1. 학습 모델의 Weight 초기화가 PE에 영향을 받는다.
2. Residual Sum 같은 효과를 준다.

특히 두 번째 가능성은 [Attention 모듈을 위한 PE를 추가한 논문](https://arxiv.org/abs/2104.08698)에서 제시한 아이디어에서 착안한 것입니다.  

### PE의 의미: 무엇이 중요한가?
Transformer와 그 이전의 언어 모델(Bahdanau 모델 등)을 살펴본 결과, 모든 논문의 공통점은 언어 모델의 성능을 높이기 위해, 각 Token 간의 상대적 위치 정보를 효과적으로 학습할 수 있도록 설계했다는 점입니다.  
결국, PE의 핵심은 이러한 상대적 위치 정보를 모델이 잘 이해하도록 돕는 데 있습니다.

### 왜 이 포스트를 작성했는가?
이전 GAN 기반 모델을 설계하면서 Uber에서 제안한 Pixel Coordinates 기반 PE를 사용했던 경험이 있습니다.  
> PE(r,g,b,x,y) = concat(Input(r,g,b), PixelCoords(x,y))  

그러나 해당 방법은 HDR Reconstruction(Super Resolution과 유사한 연구 주제) 모델에서는 기대했던 만큼의 성능 향상을 보지 못했습니다.

> 몇 주간 실험 결과가 기대와 달라 "내가 문제인가? PE가 문제인가?"라는 의문이 들었습니다. $\rightarrow$ 역시나 제가 문제였죠? [Fourier Features Let Networks Learn High Frequency Functions in Low Dimensional Domains](https://arxiv.org/pdf/2006.10739)에서 제 고민이 해결 되었습니다!  

이후 Transformer 논문을 읽으며 PE가 중요한 역할을 한다는 내용을 접했지만, 효과를 못봤던 기억도 있고 논문 내에서 짧게 설명되어있어 이해가 가질 않았기 때문에 PE의 효능에 대해 반신반의했던 기억이 있습니다.  
이번에 시간을 내어 PE에 대해 깊이 탐구하며 그동안의 의문들을 어느 정도 해소할 수 있게 되었네요!  