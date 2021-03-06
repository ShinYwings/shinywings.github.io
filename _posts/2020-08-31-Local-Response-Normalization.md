---
layout: post
title: Local Response Normalization
comments: true
categories : [Deep Learning]
tags: [Batch Normalization]
---
- Local Contrast Normalization과 유사   
    [What is the Best Multi-Stage Architecture for Object Recognition?](http://yann.lecun.com/exdb/publis/pdf/jarrett-iccv-09.pdf)

> 아... feature map은 항상 1D 인가?

- non-trainable layer

![sort-LRN](/public/asset/LRN-sort.png)
- [AlexNet]({% link _posts/2020-08-23-AlexNet.md %}) 에서 사용한 방식은 Inter-Channel LRN 이다. AlexNet 링크 참조하면 됨 
- Intra-Channel LRN:
  - the neighborhood is extended within the same channel only as it can be seen in the figure above. The foluma is given by     
        
    ![intra-channel-lrn](/public/asset/intra-channel-lrn.png)
    - inter-channel이 1차원 (depth) 만 다뤘다면, intra-channel은 2차원 (W,H) 를 다룸 $$->$$ 2D neigborhood    

