---
title: Stationarity of Statistics in terms of CV
comments: true
tags:
- Statistics

toc: true
toc_label: "Helpers"
toc_icon: "cogs"
---

[AlexNet]({% link _posts/2020-08-23-AlexNet.md %}) 논문 중에서,    
>Their capacity can be controlled by varying their depth and breadth, and they also make strong and mostly correct assumptions
about the nature of images (namely, stationarity of statistics and locality of pixel dependencies).   
   
이 있다.    

the nature of images에 대해, breadth, depth를 조절하여 Dataset의 크기를 컨트롤 할 수 있고 강력하고 정확한 assumptions을 생성할 수 있다.    
    
이 때, the nature of images의 조건 3가지,    
1. namely
2. stationarity of statistics
3. locality of pixel dependencies
라고 한다.    

세 가지 각각의 의미는,

- namely
  - an object modeller and a system that performs the matching of stored representations to those derived from the sensed image.     
  (i.e. 어떤 이미지에 대해 모델이 정확히 인식해야 한다.)
- stationarity of statistics    
  - 개인적으로 뜻을 이해하기 정말 어려웠다.
    - Wikipida 인용    
             
        > 확률 과정 $$X_t: t$$ $$\in$$ $$T$$ 에서 임의의 
        확률변수 $$X_{t_1},\dotsc,X_{t_k}$$ 에 대한 결합분포 $$F_x(X_{t_1},\dotsc,X_{t_k})$$ 이 있다고 가정하자.    
         임의의 $$\tau$$ 에 대해 $$F_x(X_{t_1+\tau},\dotsc,X_{t_k+\tau}) = F_x(X_{t_1},\dotsc,X_{t_k})$$ 를 만족하는 확률 과정 (stochatstic process) 를 의미한다.    
                  
        ```1. 시간과 상관없이 확률 변수간 확률 분포가 일정하다.```    
        ```2. 분포와 시간이 독립적이라서 확률변수의 기댓값, 분포등 또한 독립이다.```
    
    - 위키피디아는 시간의 불변성을 이야기를 한다.    
   그래서, 시간에 따른 a sequence of images, 즉 video를 생각하게 되었다.    
   하지만, stackexchange에서는 시간이 아닌 **픽셀**로 생각해야한다고 주장한다.

    - StackExchange 인용 (약간 controversial 하네)
        > 답변: stationarity of statistics might mean that the values of the pixels do not change over time, so this could be related to diffusion techniques in image processing. stationarity of statistics might also mean that the values of the pixels do not change much in a spatial neighborhood, even though, stationarity, e.g. in reinforcement learning, usually means that something does not change over time (so, if that's the case, the terminology stationarity is at least misleading and confusing, in this context), so this might be related to the locality of pixel dependencies property. Possibly, stationarity of statistics could also indirectly mean that you can use the same filter to detect the same feature in different regions of the image.    
            
        > I really think in this case stationarity isn't regarding time, but **spatial location (given no time exists-- im assuming you meant over the dataset)** I think they're saying a lot of local statistics are common across multiple areas of the image (therefore a single convolved filter can be a useful featurization of multiple fields rather than just one) – mshlis Jul 31 '19 at 1:19    

        > @mshlis You might be right. This was my interpretation, given the usual meaning of stationarity (e.g. in reinforcement learning). However, if your interpretation is correct, then their terminology is highly confusing or misleading. – nbro♦ Jul 31 '19 at 1:36    

        > I completely agree, **generally stationarity when referring to images draws on the distributions of the pixels themselves**, but here it seems to be used based on the dependencies, treating the moving indices of the convolution to parametrize some from of process. – mshlis Jul 31 '19 at 2:06    

        > Thanks for the answer! Could you edit the part about stationarity of statistics? I don't think there's any temporal aspect to image processing at least in the context of the question. I can then accept the answer once that part of edited. Thanks again! – Shirish Kulhari Jul 31 '19 at 9:19    

        > @ShirishKulhari You can view an image or a sequence of images as a flow over time, so this interpretation is not completely wrong. Have a look at certain image processing techniques that are based on diffusion. – nbro♦ Jul 31 '19 at 10:12    

        > @nbro: Oh, my bad. I can understand that a video could definitely be interpreted as a sequence of images as a flow over time, but I'm not clear on how a single, static image be interpreted as such. Could you elaborate about that in the answer, if possible? – Shirish Kulhari Jul 31 '19 at 10:19    

        > @ShirishKulhari **Essentially, a noisy image $$\hat{f}$$ of an original image $$f$$ might be viewed as an image of a sequence of images, $$f,f1,f2,\cdots,\hat{f},\hat{f^1} \cdots, $$that starts at the original image and goes through $$\hat{f}$$, so this sequence of images is part of a "flow".** **This is just an assumption or interpretation made by some image processing techniques, like anisotropic diffusion, which is used to denoise an image.** If you're interested in this, have a look at the details of this technique. – nbro♦ Jul 31 '19 at 10:21    

        > @nbro: So denoising involves figuring out the transformation $$f→\hat{f}$$ and inverting it? – Shirish Kulhari Jul 31 '19 at 10:23    

        > @ShirishKulhari Yes, that's basically the idea. – nbro♦ Jul 31 '19 at 10:24    

        ```1. 한 이미지에 대한 noise가 일정하다(pixel의 분산정도가 낮다?)는 것이라고 강화 학습에서 주로 쓰는 용어라고 한다. (stationarity of pixel distribution)```

        ```2. diffused image 같은 것들, anisotropic diffusion filter가 있다고 함. (Gaussian Filter가 영상의 엣지를 부드럽게 하는 것이라면 이거는 엣지를 더 세운다.)```     

    - i.e. 좀 더 선명해야 한다. 색이 퍼져 있음 안된다. 

- locality of pixel dependencies    
    > **neighboring pixels tend to be correlated**, while faraway pixels are usually no correlated. This assumption is usually made in several image processsing techniques (e.g. filters). Of course, the size and the shape of the neightborhood could vary, depending on the region of the image (or whatever), but, in practice, it is usually chosen to be fixed and rectangular (or squared).    

    - 옆에 있는 픽셀끼리의 서로 간의 연관성이 있어야 한다.

    ![locality](/assets/img/locality_of_pixel_dependency.png)    
        
    >아래 사진을 보면 코라는 특징은 파란색 사각형 안에 있는 픽셀값에서만 표현되고 해당 픽셀들끼리만 관계를 가진다고 볼 수 있다. 빨간색 사각형안의 픽셀들은 파란색 사각형안의 픽셀들과는 종속성이 없다.    
        
    Sparse interations 특성을 갖는 필터로 Convolution 연산을 하는 것과 잘 어울린다.     
    > Sparse interations: 하나의 출력 유닛이 입력의 전체 유닛과 연관(연결)되어 있지 않고 입력의 일부(locality) 유닛들과만  연결되어 있다라는 의미

    stationarity of statistics의 또한 Sparse interations 특성을 어느 정도 가지고 있다. 

- **궁금한 점**:    
    픽셀끼리의 서로 간의 연관성이 있어야 한다. 라는 말이 와닿지가 않는다. 이웃한 픽셀끼리 비슷한 값을 가진다라는게 correlated한 것이라고 하면, 뿌옇게 된 이미지는 locality of pixel dependencies가 좋다는 말이다. 하지만, stackexchange의 인용에 따르면 뿌옇게 된 것은 stationarity of statistics에 나쁜 것이다. 그럼 2번과 3번은 서로 trade-off 관계에 있는 것일까?

    [해결]     
    > trade-off 가 아닌 이유는 locality는 feature를 정의하는데 필요한 것이고 stationarity는 그 feature를 가지고 추측을 하는데 필요한 것이다. 그러므로, 서로 상부상조하는 관계이다. ^^    
    > Blurring 같은 경우에도 stationarity가 맞긴 하지만 여기서 이야기하는 CNN에 대한 stationarity가 아닌 이미지 프로세싱에 대한 stationarity에 대해 말하는 것 같다!    

    ![stationarity of statistics vs locality of pixel dependency](/assets/img/stationarity_of_statistics.png)     
    [추가 자료]<https://medium.com/@seoilgun/cnn%EC%9D%98-stationarity%EC%99%80-locality-610166700979>    

    > Stationarity of statistics는 시간이 변해도 패턴이 반복된다는 
    > 뜻이다.   
    > 동일한 패턴이 반복되면 분석도 훨씬 쉬워진다.     
    > ~생략~    
    > 즉, 이미지의 특정 위치에서 학습한 파라미터를 이용해서 다른 위치에 있는 동일한 특징을 추출할 수 있다는 뜻이다.     

    > <U>특정 위치에서 학습~~ 다른 위치에 있는 동일한 특징을 추출</U>    
    이 말이 CNN이 mask를 통해 이미지에서 feature를 filtering 하는 것과 동일하다. 그래서 AlexNet에서 the nature of images에 대해 설명할 때 이 단어를 넣은 것이다. (확신)     
    그러면 locality of pixel dependency에 대해서도 설명이 가능하다.    
    같은 feature를 가진 pixel들은 서로 연관성이 있어야 한다.     
            
            
- 그럼 연산과정에서 이미지의 모든 위치에서 (어느 특징을 담은)filter를 공유할 수 있어야 겠네?         
        
    CNN이 1 filter를 이미지 전체 (또는 feature map 전체)에 대해 공유를 한다. 그래서 stationarity는 feature를 공유할 수 있는 CNN과 잘 맞다고 볼 수 있다. **stationarity of statistics의 특징은 brute force하게 하던 방법(e.g. MLP)보다 비교적 연산량은 더 적고, 메모리 사용량도 적고, 통계적 효율성도 더 높다.**

    >이 Convolution연산은 이미지의 특정 부분과 필터를 내적하는것으로 볼 수도 일다. 두 벡터가 유사할수록 큰 값을 출력하는 내적의 특성에 의해 필터는 이미지에 있는 패턴들과 유사한 형태로 학습이 되는것이다.    

    <U>두 벡터가 유사할수록 큰 값을 출력하는 내적의 특성에 의해 필터는 이미지에 있는 패턴들과 유사한 형태로 학습</U> 왜 우리가 image segmentation 할때 굳이 Convolution을 사용하는지에 대해 나온다.    

    >많은 머신러닝 프레임워크에서는 필터를 반전하는 것을 제외하고는 Convolution과 완전 동일한 연산인 Cross-correlation을 구현한다. CNN에서 Convolution연산은 특징만 학습하면 되기 때문에 필터를 반전시키지 않아도 동일한 효과를 낼 수 있다.        

    마찬가지로, [CONV vs CORR]({% link _posts/2020-08-27-Correlation-vs-Convolution.md %}) 에서 본 것처럼 프레임 워크가 Convolution을 쓰는지 Cross-Correlation 쓰는지 동일한 효과를 내기 때문에 프레임워크 마다 서로 다르다고 한다.     
    (추워서 입술이 돌아가도 입술은 입술이다.)

- ### 이 블로그의 정리
    - CNN은 세가지 성질을 가지고 있다.
      1. parameter sharing
      2. translation equivariance (입력과 출력의 변화가 같다.)   
      3. Sparse interactions   
    - 그러므로, stationarity of statistics는 이미지 추출함에 있어 이 세가지 성질과 호흡이 잘 맞기 때문에 CNN에 적합한 특징이다.     
    (locality of pixel dependency는 3번째 조건 충족)

    > [참고]    
    > max-pooling은 대표적인 small translation invariance 함수이다. [1,0,0] 과 [0,0,1] 두 입력을 넣어도 동일한 1이 추출하기 때문에 모양이 약간씩 다르거나 위치가 달라진다. 이것 덕분에 segmentation하고 싶은 feature의 모양이 조금씩 다르거나 위치가 다른 경우에도 동일한 feature로 인식하게 되는 것이다. (80퍼 인정)    
         
- ### 하지만 추워서 입술이 돌아간다고 입술이 떠다니면 안된다.    
    
tralanslation invariance 효과 덕분에 좀 더 일반적인 특징을 잡을 수 있게 되었다. 그러면 translation invariance를 피하고 싶은 경우도 있을까?    
    
**각 특징들의 위치 정보가 중요한 경우, translation invariance를 막는 CNN 구조들도 있다.**    

![translation_invariance](/assets/img/translation_invariant.png)    

위의 예가 translation invariance의 단점이다.    

그래서 이 문제를 해결하기 위한 방법으로,     
    
1. Locally connected layers(unshared convolution)는 일반적인 Convolution 연산과 동일하지만 파라미터 공유를 하지 않는다. 즉 각 위치마다 다른 필터를 사용하기 때문에 동일한 특징이라도 위치에 따라 다른 필터를 학습을 하게 된다.    
결국 필터가 위치 정보를 포함한다고 볼 수 있으며, 결과적으로 translation invariance를 버리게 된다.    
(max-pooling을 사용한다면 small translation invariance 특성은 가질 수 있다.)

1. 또 다른 유명한 모델로 캡슐 네트워크가 있다. 캡슐 네트워크는 아예 새로운 방식으로 translation invariance문제를 해결한다.    

하지만 깊은 Convolutional layer 일수록 더 큰 형태의 특징들을 뽑아내기 때문에 저렇게 정확하게 눈,코,입 단위로만 따로 특징을 배워서 어떤 판단을 하지 않는다.    
**어느정도 전체 형태를 보기 때문에 각각의 작은 특징들의 위치를 아예 무시하지 않는다는 뜻이다**    

즉, **CNN의 구조를 어떻게 만드냐에 따라 scale & translation 에 대한 문제는 충분히 커버 가능하다.**     


    
[Reference]     
[https://ai.stackexchange.com/questions/13683/what-is-the-meaning-of-stationarity-of-statistics-and-locality-of-pixel-depen](https://ai.stackexchange.com/questions/13683/what-is-the-meaning-of-stationarity-of-statistics-and-locality-of-pixel-depen)
<https://medium.com/@seoilgun/cnn%EC%9D%98-stationarity%EC%99%80-locality-610166700979>
