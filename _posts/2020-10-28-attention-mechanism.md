---
title: Attention Mechanism
comments: true
tags: [Attention Map]
---

## Attention Mechanism

- 문제     
seq2seq 모델에서 처음 나왔는데, 입력 seq 를 context vector라는 하나의 고정된 크기의 벡터 표현으로 압축하고,
디코더는 context vector를 통해 출력 seq를 만들어냄.

**하나의 고정된 크기의 벡터에 모든 정보를 압축하려고 하니까 정보 손실이 발생**
**Vanishing gradient 문제 존재**

- Idea      
Decoder에서 출력 단어를 예측하는 매 시점마다 (time step), encoder에서의 전체 입력 문장을 다시 한 번 참고한다는 점입니다.
단, 전체 입력 문장을 전부 다 동일한 비율로 참고하는 것이 아니라, 해당 시점에서 예측해야할 단어와 연관이 있는 입력 단어 부분을
좀 더 집중(attention)해서 보게 됩니다.

```python
Attention(Q,K,V) = Attention Value
``` 
Q: query (t 시점의 디코더 셀에서의 은닉 상태)     
K: key (모든 시점의 인코더 셀의 은닉 상태들)    
V: value (모든 시점의 인코더 셀의 은닉 상태들)    

