---
title: Local Minima
comments: true
tags:
- Local Minima
---

- Local Minima 문제는 에러를 최소화 시키는 최적의 파라미터를 찾는 문제에서 파라미터 공간에 수많은 지역적인 hole들이 존재하여 이러한 local minima에 빠질 경우 global minimun을 찾기 힘들게 되는 문제를 말한다.
## 용어 정리

- Local Min, Max vs Global Min, Max    
    ![local global](/assets/img/LocalMinima/local_global.jpg)

    - 2차 미분값이 positive 값을 가지면 local minimum (여러 개 minima)
    - 2차 미분값이 negative 값을 가지면 local maximum (여러 개 maxima)
    - Hessian 행렬이 positivie definite (convex) 인 경우, global minimum
    - Hessian 행렬이 negative definite (concave) 인 경우, global maximum    
    - local minima를 주로 찾는다.
    
- Stationary Point(Critical Point) (정상점)   
    ![Stationary Point](/assets/img/LocalMinima/stationary_points.jpg)  

    - Stationary Point = Local Minima & Maxima + Global Minimum & Maximum + Saddle Point    

- Saddle Point (안정점)
    
    - convex도 concave도 아닌 stationary point   
    ![Saddle Point](/assets/img/LocalMinima/saddle_point.jpg)   

    - $$f' = 0 (critical point), f''=0$$인 경우, saddle point일 수도 아닐 수도 있다.
    - 변곡점을 찾자(?)
    - multiple dimension인 경우, 2차원에서는 local minima 였던 것들이 saddle point 일 수도 있다.    
    - 딥 러닝 경우, 예전에는 성능 저하의 원인이 local minima라는게 일반적인 생각이였다. 그러나 [Y. Dauphin, R. Pascanu, C. Gulcehre, K. Cho, S. Ganguli, Y. Bengio. "Identifying and attacking the saddle point problem in high-dimensional non-convex optimization"](https://arxiv.org/pdf/1406.2572.pdf)에서, 차원이 엄청 많기 때문에, local minima가 생기는 경우는 극히 드물다고 한다.      
    > 왜냐하면, 여러 개의 차원에서, 어떤 critical point가 local minima가 되기 위해서는 모든 축의 방향으로 함수 그래프가 아래로 볼록($$f''>0$$)이 되어야만 한다. 그러나 수백개의 차원에서, 실제로 모든 변수가 이 조건을 만족해 local minimum을 이루는 경우가 과연 얼마나 있을까?
      - n차원의 다변수 함수 $$f(x_1, x_2, \cdots, x_n)$$이 있을 때, local minima를 만족하기 위해서는 다음의 조건을 필요하다.    
            $$\frac {\partial f} {\partial x_i}=0, i=1,2,\cdots,n$$    
            $$\frac {\partial ^2 f} {\partial ^2 x_i} >0, i=1,2,\cdots,n$$
      - **Hessian Matrix는 Symmetric하다.**([헤시안 행렬이 대칭인 이유](https://www.quora.com/Why-is-the-Hessian-matrix-symmetric)) 그래서 diagonal로 표현이 가능하다.
        그럼 Hessian 행렬은 positive definite 라면,    
        $$\frac {\partial ^2 f} {\partial ^2 x_i} >0, i=1,2,\cdots,n$$    
        이고, $$f_i'' >0$$ 일 확률은 0.5이다.    
        그럼 $$f_i'', \cdots, f_n''$$ 이 모두 positive일 확률은 $$\frac 1{2^n}$$ 이고,     
        $$\frac 1 {2^n}$$는 global minimum일 확률이다.
    - 그래서 학습 속도를 최적화할 때, 거의 대부분은 saddle point이며 우리는 saddle point를 극복할 수 있는 새로운 탐색 방법을 구색해야 한다.     
    - 또한, **local minima가 발생한다 하더라도 이는 global minimum이거나 global minimum과 거의 유사한 수준의 에러 값을 가진다.**    
    - 그래서 대부분 local minima를 찾는 것 같다.    
    - 말 안장(saddle)을 생각하면 편하다.    
        
    ![Saddle Point](/assets/img/LocalMinima/saddle_matlab.jpg)           
            
                
        
            
## 그럼 최종 목적지는 어느 점으로 잡는게 좋을까?    

- SGD + Momentum을 사용하여 학습 성능을 최적화할 때, local minima 지점을 거의 도달했는데도 왔다 갔다 하는 모습을 볼 수 있다. (cs231n 7장 강의 참조)    
- 이것은 버그가 아니라 SGD+Momentum이 very sharp minima들을 스킵하는 현상이다.

- Flat Minima VS Sharp Minima    
        

    > Hochreiter & Schmidhuber (1997) defines a flat minimum
    > as "a large connected region in weight space where the
    > error remains approximately constant".

    sharp minima can overfits more, so we don't want to land sharp minima.

    flat minima is robust

    ![sharp minima](/assets/img/LocalMinima/sharp_minima.jpg)    

    - Sharpness Definition
    ![sharpness](/assets/img/LocalMinima/sharpness.jpg)
        
    [L Dinh, R Pascanu, S Bengio, Y Bengio. "Sharp Minima Can Generalize For Deep Nets"](https://arxiv.org/pdf/1703.04933.pdf)을 참조해서 다시 작성해야함 (대충 훑어보고 자세히 읽지는 않음)


## Reference    
[https://darkpgmr.tistory.com/148?category=761008](https://darkpgmr.tistory.com/148?category=761008)
[https://hwiyong.tistory.com/9](https://hwiyong.tistory.com/9)

### 나는 용어 참조만 하고 싶었기 때문에, 깊이 파보진 않았다. 자세히 알고 싶으면 두 블로그에 정리 엄청 잘 되어있다. 
