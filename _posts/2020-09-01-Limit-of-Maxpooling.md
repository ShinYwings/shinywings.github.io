---
title: Maxpooling의 한계

categories:
- 개념 정리
tags:
- Deep Learning
- Maxpooling
- CapsNet
- AlexNet
  
comments: true
toc: true

---
<https://jayhey.github.io/deep%20learning/2017/11/28/CapsNet_1/>
- 이 블로그를 읽고 느낀 점 (CapsNet에 관한 내용임)   

    - [translation invariance]({% link _posts/2020-08-26-stationarity-of-statistics.md %}) 조건으로 feature을 discard (maxpooling) 하느냐 아니면 translation equivariance할 수 있는 다른 새로운 방법을 찾아 disentangling 하냐에 대해서 논란이 있다.     
    확실히 disentangling하는 것이 더 나은 방법이라고 생각한다. 객체 지향처럼 encapsulation이라는 개념이 소개 되었는데, 세상의 모든 성질을 객체에다가 넣어서 평가하려면... 불가능함에 가까워 보인다. 이런 부분을 고려해서 CapsNet 구조를 만들었을 것이다. CapsNet을 아직 안봐서 이 부분은 나중에 내 생각의 수정이 필요하다.

    - maxpooling의 잘못된 점
      1. 내재되어있는 선형적인 구조를 사용하지 않는다. (Fails to use underlying linear structure).    
        - SIFT를 통해서도 알겠지만 여러 방면으로 3D 물체를 돌려보는 것은 비선형적이다. 이를 globally linear 한 곳으로 변환시킨다면 대량의 extrapolation이 가능합니다. 즉, 물체의 다양한 모습을 추정 가능하다는 말과 같습니다. 하지만 pooling에는 이러한 과정이 없습니다.
      2. Dynamic routing과는 어울리지 않음
        - pooling에서는 캡스넷(캡슐 네트워크)의 가장 큰 특징 중 하나인 dynamic routing을 사용할 수 없다.
    - 현재 CNN의 주요한 문제점
        > Internal data representation of a convolutional neural network doesn't take into account important spatial hierarchies between simple and complex objects"    
        - CNN 내부 데이터의 간단한 오브젝트와 복잡한 오브젝트 사이의 spatial hierarchies를 고려하지 않는다.    

    - CapsNet은 feature간의 공간적인 관계까지 고려하게 됩니다.
      - 그럼 feature 간의 weight 또한 필요하다고 볼 수 있네
    - Maxpooling은 학습 가능한 파라미터가 존재하지 않으며 단순히 가장 큰 특징적인 값만 뽑아내서 FOV를 넓히는 것 밖에 못한다는 것입니다.
      - 하지만 캡슐은 벡터로 이루어져 있기 때문에 dynamic rouing을 통해 오브젝트 파트들의 상대적 위치를 조합할 수 있음     

맥스풀링은 깐깐함에서 좀 더 유연하게 풀어주는 역할을 한다.     
유연함은 필요하지만 가지고 있는 특징들을 버리면서까지 유연함을 유지하는 것은 좋지 않은 방법이라는 말에 동의한다.     
Maxpooling 말고 PCA를 통해 줄여도 똑같겠지? ㅎㅎ;
