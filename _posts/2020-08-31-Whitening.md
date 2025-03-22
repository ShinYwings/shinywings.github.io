---
title: Whitening
comments: true
tags: [NormalizationNormalization]
---

input mean:0, $$\sigma^2$$: 1, 그리고 decorrelated 한 것을 의미    
    
이 조건을 만족하면 더 빨라진다!    
    
하지만 공분산 계산하는 computationally cost가 높다.    

Batch Normalization 논문에 따르면, bias가 무한대로 증가할 가능성이 있어 loss update가 안된다고 한다. 그래서 꼭 좋지만은 않은 것이다.    
왜 그런지는 좀 더 논문을 살펴봐야 알 것같다.