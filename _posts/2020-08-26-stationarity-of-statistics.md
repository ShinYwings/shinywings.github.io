---
layout: post
title: Stationarity of Statistics in terms of CV
comments: true
categories : [Others]
tags: [AlexNet]
---

[AlexNet]({% link _posts/2020-08-23-AlexNet.md %}) 논문 중에서,    
>Their capacity can be controlled by varying their depth and breadth, and they also make strong and mostly correct assumptions
about the nature of images (namely, stationarity of statistics and locality of pixel dependencies).   
   
이 있다.    

the nature of images에 대해, breadth, depth를 조절하여, Dataset의 크기를 컨트롤 할 수 있고 강력하고 정확한 assumptions을 생성할 수 있다.    
    
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

- **궁금한 점**:    
    픽셀끼리의 서로 간의 연관성이 있어야 한다. 라는 말이 와닿지가 않는다. 이웃한 픽셀끼리 비슷한 값을 가진다라는게 correlated한 것이라고 하면, 뿌옇게 된 이미지는 locality of pixel dependencies가 좋다는 말이다. 하지만, stackexchange의 인용에 따르면 뿌옇게 된 것은 stationarity of statistics에 나쁜 것이다. 그럼 2번과 3번은 서로 trade-off 관계에 있는 것일까?    
  
[Reference]     
[https://ai.stackexchange.com/questions/13683/what-is-the-meaning-of-stationarity-of-statistics-and-locality-of-pixel-depen](https://ai.stackexchange.com/questions/13683/what-is-the-meaning-of-stationarity-of-statistics-and-locality-of-pixel-depen)