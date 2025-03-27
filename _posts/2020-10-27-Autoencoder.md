---
title: Autoencoder
categories:
- Deep learning
tags:
- Auto encoder

comments: true
toc: true
---

## Definition

a neural network that is trained to attempt to copy its input to its output.

Encoder(deterministic functions): $$h = f(x)$$     
Decoder(stochastic mappings): $$r = g(h)$$     

So, learning to set $$g(f(x)) = x$$

However, autoencoders are designed to be **unable** to learn to copy **perfectly**    
     
Objective : h taking on useful properties(features).    
> useful featrues from the autoencoder is to constrain $$h$$ to have a smaller dimension than $$x$$.    

Usage : feature learning (feature extraction), dimensionality reduction     

## Modern Autoencoders    
Encoder => Deterministic functions : $$p_{encoder}(h|x)$$     
Decoder => Stochastic mappings : $$P_{decoder}(x|h)$$     

So, autoencoders + latent variable models => generative modeling.

- Latent Variable(as opposed to observable variables)    
 := the variables that are not directly observed but are rather inferred from other variables that are observed.

- Autoencoder training    
    - minibatch gradient descent
    - **recirculation**     
        := a learning algorithm based on comparing the activations of the network on the original input to the activations on the reconstructed input.
     
The learning process    
    
$$min({L(x,g(f(x)))})$$     
     
$$L$$ is a loss function penalizing $$g(f(x))$$     

## Undercomplete Autoencoders   
an autoencoder whose hidden code ($$h$$) dimension is less than the input dimension.    

- undercomplete with (decoder = linear), ($$L$$ = MSE)     
    - learns to span the same subspace as **PCA**
    - trained to perform the copying task has learnt the princple subspace of the training data as a **side effect**

- undercomplete with (encoder = nonlinear), (decoder = nonlinear)     
    - learn a more **powerful nonlinear generalization of PCA**      

- **DON'T allows too much capacity**    
    -about the distribution of the data, feature extracting 대신에 copying 함

## Overcomplete Autoencoders     
an autoencoder whose hidden code ($$h$$) dimension is greater than the input dimension.

- overcomplete with (encoder = linear), (decoder = linear)
    - learn to copy the input to the output without learning anything useful about the data distribution.

## Regularized Autoencoders
get ability to **choose the code dimension and the capacity** of the encoder and decoder based on the complexity of distribution to be modeled.     
     
Rather than limiting the model capacity by keeping the encoder and decoder shallow and the code size small.     

Objective : use a loss function that encourages the model to have other properties include sparsity of the representation, smallness of the derivative of the representation, and robustness to noise or to missing inputs.
    
- Regularized + Overcomplete = Undercomplete     
(in terms of feature extraction)     
    - can be nonlinear and overcomplete but still learn something useful about the data distribution, even if the model capacity is great enough to learn a trivial identity function.

## Particular form of Autoencoder (regularized autoencoders 관련된 모델들)
1. nearly any generative model with latent variables
2. equipped with an inference procedure (for computing latent representations given input)
- Two generative modeling approaches that emphasize this connection with autoencoders are **the descendants of the Helmholtz machine**

    - Helmholtz machine
        1. variational autoencoder (section 20.10.3 in deep learning book)
        2. generative stochastic networks (section 20.12 in deep learning book)
        - These models naturally learn high-capacity, overcomplete encodings of the input and do not require regularization for these encodings to be useful.
        - there encodings are naturally useful because the models were trained to approximately maximize the probability of the training data rather than to copy to input to the output.

## Sparse Autoencoders
an autoencoder whose training criterion involves a sparsity penalty $$\Omega(h)$$ on the code layer $$h$$, in addition to the reconstruction error     
     
$$L(x,g(f(x))) + \Omega(h)$$    

usage : learn features for another task (e.g. classification)

- copying task(vanila autoencoder) + sparsity penalty 목적
    - yield a model that has learnt useful features(unique statistical features) as a byproduct.

- $$\Omega(h)$$ is sort of Regularizer
    - However, there is not a straightforward Bayesian interpretation

- training with weight decay(= regularizer) and other regularization penalties can be intpreted as a MAP approximation to Bayesian inference, with the added regularizing penalty corresponding to **a prior probability distribution** over the model parameters.     
         
    $$\begin{equation} \begin{split} i.e.\;\; MAP\;approxiamtion &\approx Bayesian\;inference + regularizing\;panelty \\    
                      &\approx training + regularizing\;penalty \end{split}\end{equation}$$     
In this view, $$\begin{equation}\begin{split} regularized\;maximum\;likelihood &= max\;p(\theta|x) \\
        &= max\;(MAP\;Estimation(p(\theta|x))) \\
        &= max\;(log(MAP(p(\theta|x)))) \\
        &= max\;(log\:p(x|\theta)p(\theta)) \\ 
        &= max\;(log\:p(x|\theta)+log\;p(\theta)) \\
        &Equivalent,\\
        &= max\;(log\;likelihood + prior\;distribution) \\ \end{split}\end{equation}$$
        where,    
            $$p(\theta|x)$$ is conditonal probability    
            $$p(x|\theta)$$ is likelihood    
            all $$\theta$$ is independent    
            **prior distribution can implicitly expressed as a preference over functions**
    - MAP inference using a gaussian prior on W (Maximum A Posteriori)    

        - Bayes theorem (conditional probability)     
            evidence  = (likelihood * prior P) / (posterior P)
            
        $$p(x|z) = \frac {p(x \cap z)} {p(z)}  = \frac {p(z|x)p(x)} {p(z)}$$    
              
        - MAP method    
            - posterior P를 최대화 시킨다.    
            - 장점: leveraging information that is brought by the prior and cannot be found in the training data.    
                (this additional information helps to reduce the variance in the MAP point estimate)
            - 단점: the price of increased bias    
                 
            (e.g.)     
            z = 머리카락 발견    
            남자것일 확률: $$p(male|z)$$    
            여자것일 확률: $$p(female|z)$$     
                    
            비교 후, 큰 값을 갖는 클래스(성별) 찾는 방법         
                    
            $$\begin{equation} \begin{split}
            p(female|z) &= \frac {p( female \cap z)} {p(female \cap z) + p(male \cap z)} \\     
            &= \frac {p(z|female)p(z)} {p(z|female)p(z) + p(z|male)p(z)} \\
            &\;Since,\;the\;summation\;of\;all\;probablities\;is\;1      \\
            &(male\;and\;female\;are\;independent) \\
            &= p(z|female)p(z) 
            \end{split}\end{equation}$$
            
            | Maximum likelihood | Maximum A Posteriori |
            | ------------------ | -------------------- |
            | class 비율 고려X   | class 비율 고려O     |

        - MAP Estimation
            choose the point of maximal posterior probability    
            $$\theta_{MAP} = arg\;max_\theta\;p(\theta|x) = arg\;max_\theta\;log\:p(x|\theta) + log\:p(\theta)$$

However, Regularized autoencoders defy such an interpretation because the regularizer $$\Omega(h)$$는 데이터에 의존하므로 정의상 단어의 공식적인 의미에서 **a prior**이 아닙니다.    
So, Rather than thinking of the sparsity penalty as a regularizer for the copying task, we can think of the entire sparse autoencoder framework as **approximating maximum likelihood training of a generative model that has latent variables.** Again,    
- explicit joint distribution $$p_{model}(h,x)=p_{model}(h)p_{model}(x|h)$$    
where, h is latent varibles and x is visible variables    
- $$p(\theta)$$ encodes our beliefs about the model's parameters before we have seen the training data.    
     
Thus, log-likelihood can be decomposed as     
    
$$log\;p_{model}(x)=log\displaystyle \sum_h p_{model}(h,x)$$

> p(x)가 exp함수이면 log likelihood의 softmax의 loss 함수 꼴

- 이러면, autoencoder를 approximating this sum with a point estimate for just one highly likely value for $$h$$    
(sparse coding generative model이랑 비슷하다)
