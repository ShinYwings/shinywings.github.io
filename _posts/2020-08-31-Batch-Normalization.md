---
layout: post
title: Batch Normalization
comments: true
categories : [Deep Learning]
tags: [Internal Covariate Shift]
---

Trainable layer normally used for addressing the issues of **Internal Covariate Shift(ICF)**    

## Internal covariate shift   
- hidden layer들의 covariate shift를 의미 
- hidden neurons이나 activation의 분포가 변화하는 것 때문에 고려된다.            
- layer는 계속해서 새로운 분포를 적용시켜야한다.     
     
     ![ICF](/public/asset/ICF.png)    
    - Say we have trained a neural network, and now we select two significantly different looking batches from the dataset for inference (as shown above).    
- 2 batches를 각각 forward pass를 시켜 hidden layer의 feature map을 plot하면 위의 그림처럼 엄청난 분포의 변화가 발생한다는 것을 알 수 있다.   
- 이것을 **Covariate shift** 라고 부른다.    
-  During training, if we select batches that belong to different distributions then it slows down the training due to the fact that for a given batch it tries to learn a certain distribution, which is different for the next batch.    
-  Hence it keeps on bouncing back and forth between distributions until it converges. This Covariate Shift can be mitigated by making sure that the members within a batch do not belong to the same/similar distribution.        
     
- 우리가 hidden neurons의 분포를 직접 control 하지 못한다는 문제가 있음    
- 왜냐하면 훈련이 훈련 패러미터들을 계속 업데이트하기 때문이다.    
- BN은 이 문제를 완화시켜줌.      
Batch Normalisation만 가지고는 우리의 방법의 full advantage를 얻기 힘들다.    

[Reference](https://towardsdatascience.com/difference-between-local-response-normalization-and-batch-normalization-272308c034ac)    

-------------

In the paper of BN,    
    
network와 its training parameters를 바꿔야 한다.    

1. Increase learning rate    
    - higher learning rate로 부터 training speedup을 부작용 없이 이뤄냄
2. Remove Dropout    
    - BN은 Dropout의 목표를 충족시켰다. in Sec 3.4
    - 수정된 BN-Inception로 부터의 dropout은 훈련 속도를 증가시켰다. without increasing overfitting.
3. Reduce the $$L_2$$ weight regularization
    - While in Inception an $$L_2$$ loss on the model parameters controls overfitting, in Modified BN-Inception the weight of this loss is reduced by a factor of 5.(5배 가량 이 loss의 가중치가 감소)
4. Accelerate the learning rate decay
    - In training Inception, learning rate was decayed exponentially.    Because our network trains faster than Inception, we lower the learning rate 6 times faster. (learning rate를 6배 빠르게 감소시켰다(?) 그래서 learning rate가 exponentially 하게 감소 되었다.)
5. **Remove [Local Response Normalization]({% link _posts/2020-08-31-Local-Response-Normalization.md %})**
    - while Inception and other networks benefit from it, we found that with Batch Normalization it is not necessary. 
6. Shuffle training examples more thoroughly (training examples을 더 잘 섞음)    
    - shard (DB) 안에서 training data가 잘 섞여서, mini-batch에서 항상 같은 examples이 나오는 것을 방지함.
    - 이것은 1%의 validation accuracy를 증가시킴. (which is consistent with the view of Batch Normalization as a regularizer: the randomization inherent in out method should be most beneficial when it affects an example differently each time it is seen.)
7. Reduce the photometric distortions.
    - BN networks는 빠르게 학습하고 각각의 학습 example을 fewer times 관찰하기 때문에, trainer focus를 더 "real"한 (덜 distorting함으로써)이미지에 맞춘다. 

## Reference
<https://ml-dnn.tistory.com/6>    
Internal covariate shift와 함께 정리 잘된 논문을 정리
<https://dalpo0814.tistory.com/18> BN 논문 정리