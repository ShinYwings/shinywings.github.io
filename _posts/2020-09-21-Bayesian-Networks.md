---
title: Bayesian Networks

categories:
- 개념 정리
tags:
- Artificial Intelligence
- Bayesian Networks

comments: true
toc: true

---
- 확률 이론 + 그래프 이론
- Directed Acyclic Gragh (DAG)
    > Cycle을 이루면 안된다.    
    > cycle을 검사하는 MST 알고리즘에서 cycle을 검사하는 방법을 알 수 있다.    

- Edge represents Influence as Random Variable(확률 변수) ($\approx$ State, Node) 그리고 모든 Edge들을 통해 <U>a set of conditional independence</U> 를 간결하게 표현했다.    
![condition](/assets/img/BayesianNetwork/condition.jpg)

- Conditional Probability Table (CPT)  
    Random Variable $X_i$와 부모 노드들과의 확률 분포를 나타낸다.
    ![conditioanl probability table](/assets/img/BayesianNetwork/Conditional_probability_table.png)

-------

- **joint distribution**

    - 모든 정보를 알때 유용함 (하지만 그럴 일은 없으므로 베이지안 네트워크가 유리)
    - conditional independence statements를 나타냄    

        > Joint Distribution을 나타내기 위해서 $2^n$개가 필요함     
        > 하지만, Bayesian network의 표현은 $n\cdot 2^k$ 개만 있으면 된다.    
        > 여기서 **k는 argmax(각 노드에 할당된 부모의 갯수)**      
        > 이와 같이 poly tree의 경우 계산 비용이 전체 네트워크 크기에 선형적으로 증가한다.(근데 이런 경우는 극히 드뭄)      
        > 일반적으로 베이지안 네트워크의 정확한 값의 추론은 NP-Hard 문제이다.       
        > (실제로는 #P-hard문제임, 근데 #p-hard문제가 뭔지 모르겠다.)       
        그래서 n=30 일때, Joint distribution은 1B 개의 숫자가 필요하지만, 베이지안 네트워크는 960개만 있으면 된다. 메모리를 팍 줄였다^^
    - Joint Distribution $P(x_1, \ldots, x_n)$ represents the multiplication of all possibles     
    $P(x_1, \ldots, x_n) = \prod^{n}_{i=1} P(x_i|parents(X_i))$    
        
- **Constructing a Bayesian Network**
    - Chain rule:
        $$\begin{equation} \begin{split}P(x_1, \ldots, x_n) &= P(x_n|x_{n-1},\ldots,x_1)P(x_{n-1}|x_{n-2},\ldots,x_1)\cdots P(x_2|x_1)P(x_1)\\&= \prod^{n}_{i=1} P(x_i|parents(X_i))\end{split}\end{equation}$$
    - Bayesian Networks:    
        $$P(x_1, \ldots, x_n) = \prod^{n}_{i=1} P(x_i|parents(X_i))$$  
        $x_i$ 는 이벤트
    - **베이지안 네트워크의 the ordering of nodes는 resulting network의 complexity를 나타낸다.**
        
- 관측 가능한 정보가 그래프의 위 또는 아래에 존재하는지에 따라 두가지 추론으로 나뉜다.
    1. 관측 가능한 값들을 알려지지 않은 결과 값들을 예측하는데 사용할 수 있다면, **top-down 추론** 또는 **예측** 한다.
    2. 결과 값들이 알려져 있지만 원인(관측 값)들을 알 수 없을 때, **bottom-up 추론** 또는 **진단** 한다.
    - 두 가지 경우 모두에서 관측 가능한 노드들로 인해 알려진 정보를 사용해서 **은닉** 노드들의 값을 추론해 낸다.    

    (e.g.)    
    ![bayesian networks](/assets/img/BayesianNetwork/bayesian_network.jpg)
    Top-down 추론 (예측) 같은 경우에는 관측 가능한 값들을 가지고 Mary Calls의 발생을 (TRUE) 예측하고 싶다.
    P(MaryCalls | JohnCalls, Alarm, Earthquake, Burglary)    
    = P (MaryCalls | Alarm) 으로 **제거(marginalise)**할 수 있다.     
    왜냐하면 $\prod^{n}_{i=1} P(x_i|parents(X_i))$ 에 따라     
    조건부 독립 상태에 따라 JohnCalls, Earthquake, Burglary는 Mary Calls에 대해 직접적인 영향을 미치지 못하기 때문이다.    

    bottom-up추론 (진단) 같은 경우에도 Mary Calls가 발생(TRUE)했는데 그 원인을 모를 때 원인을 알고 싶다.  
    반대 입장에서 생각하는 것이므로 Bayes' rule을 사용한다.
    $$\begin{equation} \begin{split} P(A|j,m) &= \alpha \displaystyle \sum_{b,e} P(A,j,m,b,e) \\ 
    &= \alpha \displaystyle\sum_{b,e} P(b)P(e)P(A|b,e)P(j|A)P(m|A) \\ 
    &= \alpha P(j|A)P(m|A) \displaystyle\sum_{e} P(e) \displaystyle\sum_{b} P(b)P(A|e,b) \\
    &= \alpha P(j|A)P(m|A) \displaystyle\sum_{e} P(e) \lbrace P(b)P(A|e,b) + P(\neg b)P(A|e, \neg b) \rbrace \\
    &= \alpha P(j|A)P(m|A) \lbrack P(e) \lbrace P(b)P(A|e,b) + P(\neg b)P(A|e, \neg b) \rbrace \\
    &+ P(\neg e) \lbrace P(b)P(A| \neg e, b) + P(\neg b)P(A| \neg e, \neg b) \rbrace \rbrack\\
    &= \alpha (0.9)(0.7)\lbrack (0.002) \lbrace (0.001)(0.95)+(0.999)(0.29) \rbrace + (0.998) \lbrace (0.001)(0.94) + (0.999)(0.001) \rbrace \rbrack \\
    &= \alpha (0.0004987417790000001)   
    \end{split}\end{equation}$$     
    $\neg A$  yields $\alpha * 0.000498741779$    
    $$P(A|j,m) = \alpha \langle 0.00158535846, 0.000498741779 \rangle \approx \langle 0.953909991039174, 0.30009286728551704 \rangle$$    
    j,m이 주어졌을 때 Alarm이 울릴 확률 약 95%, Alarm이 안 울릴 확률 약 30% 이다.       

    * where $\alpha$ is normalizing vector(constant)    
    * P(j,m\|A)는 j,m이 서로 독립이기 때문에 P(j\|A)P(m\|A) 로 나눠줄 수 있다.     


    > 합이 100%가 안되서 처음에 좀 놀랐다.      
    > 각각 j,m의 4가지 경우일때 각각의 True값과 False 값들을 곱해준 것이다.
    
    
- Local Markov Assumption    
    ![local_markov_assumption](/assets/img/BayesianNetwork/local_markov_assumption.jpg)
    - $X \perp NonDesc(X)\|Parent(X)$     
        : X는 1촌인 부모하고만 연관이 있다. 나머지는 다 Independent ($\perp$)
- Markov Blanket (Markov Boundary)      
    ![markov_blanket](/assets/img/BayesianNetwork/markov_blanket.jpg)
    - (a)는 markov blanket이 아니다.
    - (b)는 markov blanket 이다.    
        U: parents Z: co-parents Y: Children
    - **각 노드들은 parents, co-parents, children 만 고려하면 된다.**
