---
title: Local Response Normalization
categories:
- 개념 정리
tags:
- Deep Learning
- Local Response Normalization
- Batch Normalization

comments: true
toc: true
---
- Local Contrast Normalization과 유사   
    [What is the Best Multi-Stage Architecture for Object Recognition?](https://ieeexplore.ieee.org/document/5459469)

> 아... feature map은 항상 1D 인가?

- non-trainable layer

![sort-LRN](/assets/img/LRN/LRN-sort.png)
- [AlexNet]({% link _posts/2020-08-23-AlexNet.md %}) 에서 사용한 방식은 Inter-Channel LRN 이다. AlexNet 링크 참조하면 됨 
- Intra-Channel LRN:
  - the neighborhood is extended within the same channel only as it can be seen in the figure above. The foluma is given by     
        
    ![intra-channel-lrn](/assets/img/LRN/intra-channel-lrn.png)
    - inter-channel이 1차원 (depth) 만 다뤘다면, intra-channel은 2차원 (W,H) 를 다룸 $\rightarrow$ 2D neigborhood    

