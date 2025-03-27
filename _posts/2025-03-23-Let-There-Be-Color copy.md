---
title: (ECCV 2014) Let There Be Color! Large-Scale Texturing of 3D Reconstruction
categories:
- Paper Review
tags:
- Texturing
- OpenMVS
- 3D Reconstruction

comments: true
toc: true
---

### 발표 자료 및 논문 원본
[2021-04-16_let_there_be_color.pptx](/assets/doc/LetThereBeColor/2021-04-16_let_there_be_color.pptx)

[2021-04-23_let_there_be_color_src_code.pptx](/assets/doc/LetThereBeColor/2021-04-23_let_there_be_color_src_code.pptx)

[let_there_be_color.pdf](/assets/doc/LetThereBeColor/let_there_be_color.pdf)

---

- texture atlas를 어떻게 생성할까? 관점에서 공부하기 위한 논문
- openMVS에서 texture mapping할 때 사용한 texturing framework (large-scale)

1. Introduction (이 기술의 어려움과 이전 기술들의 한계를 중점)
    - MVS 사용한 SfM model의 large-scale datasets을 다루는 First unified texturing approach
    - Overview of Texturing a 3D model from multiple images
        - Step 1. View Selection
            
            각 face마다 어떤 view를 사용할 것인지 선택
            
        - Step 2. Texture Optimization
            
            근접한 texture끼리 매끈하게 만들기
            
    
    - **이 논문의 목표**
        - Low data cost (for large datasets)
        - High resolution (High-resolution images)
    
    - image로부터의 texture 기술의 어려움
        - illumination 문제
        - exposure changing 문제
        - non-rigid scene parts
        - unreconstructed occluding objects
        - several orders of image scales (클로즈업된거부터 멀리 떨어진 이미지까지)
        
    - large # of input images의 문제점
        - image scale
        - blur (out of focus)
        - exposure variation
        - occluders (moving plants or pedestrians)
        
    - 이전 기술의 특징
        - 소규모의 통제된 데이터 세트에 중점을 둠
        - architectural scenes 같은 특수한 경우를 handle하는 (주목할만한) 경우도 있음
        - [10] city-scale reconstruction & texture 를 구현
            - 2.5D로 구현
            - 건물 표현이 제한적임
        - [19] large 3D models with planar surfaces (빌딩같은것들)
            - SfM의 input images로부터 단서를 얻어 텍스처 구현
            - Planar 케이스만 고려 (나무 X)
            - Occlusion들은 사용자가 직접 마크해줘야했음
        - [22] interacitve texture mapping for building facades(front scene of the building)
        - [21] ToF cam 사용해서 depth 문제 해결
        - 
    - 이전 기술의 한계
        - texturing reconstruction이 있기 전
            - low resolution으로 render하면 input image를 구별하기 힘듬
            - color information이 정점 별 색상으로 인코딩되므로 mesh 해상도와 결합됨
        - texture reconstruction 해결하는 시작 단계
            - 모든 이미지의 평균치를 사용
                - visual fidelity (영상 충실도, 얼마나 진짜 같은지)가 insufficient하다.
            - Estimating lighting parameters per input image & per-vertex reflectance parameters.
                - Still, they use per-vertex colors
                
                <aside>
                ⚠️ **per-vertex colors는 mesh resolution에 제한이 있음.**
                
                </aside>
                

---

2.   Related Work

(1) View Selection

- 이전 방식들
    1. 각 face 마다 multiple view 선택 & blending [5,13]
        - Callieri et al. [5]의 방법 (목표와 반대인 사례)
            - 각도(angle), 모델에 대한 근접성(proximity to the model) 및 깊이 불연속성에 대한 근접성 (proximity to the depth discontinuities) 과 관련하여
                - compute
                    - 텍스처링을위한 마스크의 곱(입력 이미지 픽셀의 적합성을 나타내는)으로 블렌딩에 대한 weight 계산
                - not compute
                    - real textures
                - suggest
                    - vertex colors의 사용 (in combination with mesh subdivision)
        - Grammatikopoulos et al. [13] 의 방법
            - angle & proximity to the model 기반으로 pixels 블렌딩
            - 특정 뷰 (projection된 방향)에서 어느 3D surface point가 에서 보이는지 식별하고
            - 그 surface를 바라보는 모든 이미지의 텍스처를 혼합 (혼합할 때 texture color의 outlier가 들어오게 되면 자동으로 제거해주는 방법이 같이 들어감)
                
                ![](/assets/img/LetThereBeColor/Untitled.png)
                
    2. 각 face 마다 single view 선택 & blending [9,10,15,23]
        - **Lempitsky and Ivanov 방법** [15] (이 논문의 seam leveling 기반이 되는 방법)
            - face 마다 single view 선택
                - a pairwise markov random field 기반
                    - data term
                        - texturing에 쓸 view들의 quality를 judge함
                            - data term + additional effects
                                - Allene [2] 방법
                                - Gal [9] 방법
                    - smoothness term
                        - texture patch간의 seam의 정도를 나타내기 위해 모델링
    3. 각 texel 마다 single view 선택 & blending [19]
    4. hybrid method [2]
        - 각 face 마다 single view 선택
        - 가까운 texture patch borders 마다 blending
        - View-dependent texturing approaches (lumigraph) (이게 hybrid 방법인지 확실못함)
            - view들을 블렌딩함
            - lumigraph 방법 [4]  ([aaaas](https://cs.harvard.edu/~sjg/papers/ulr.pdf))
                - lumigraph는 light field rendering과 비슷한 알고리즘
                - light field
                    
                    ![](/assets/img/LetThereBeColor/Untitled%201.png)
                    
                    - 정적인 scene에서 차단되지 않은 공간을 통과하는 빛의 흐름을 정의
                    - 어떤 scene geometry를 assumption 할 수 있다고 함.
                    - light field camera
                        
                        ![](/assets/img/LetThereBeColor/Untitled%202.png)
                        
            
            [aaaas](https://www.youtube.com/watch?v=za4HIll9N7c)
            
- Blending images
    - 필요한 이유
        - 고스트, seam 현상 발생
            - 원인 : MVS settings
                - camera parameters 나 reconstructed geometry가 안맞으면 texture patch의 border도 안맞음
                - 3D 모델이 low resolution
                - 3D 모델이 완벽하게 생성되지 않았을 때
                - realistic MVS datasets 안에서 이미지 끼리 scale 차이가 너무 심할 때
    - 효과
        - weight가 smoothing 되고 scale이 모두 맞춰짐.
        - 이미지의 quality loss 발생하는데 이미지들이 모두 resample 되서 common coordinate frame에 들어가기 때문 (atlas?)
            - 이 논문에서 목표
                - Low data cost (for large datasets)
                - High resolution (High-resolution images)

(2) Color Adjustment

View Selection 하고 난 뒤 texture patches 결과의 문제점

- Color discontinuities
    - 원인
        - exposure and illumination differences 문제
        - 서로 다른 camera response curves
    - 해결
        - locally
            - Velho and Sossai 방법 [23]
                - 왼쪽 & 오른쪽 patch의 mean으로 seam smoothing
                - mean을 가지고 smooth color transition할 때 **heat diffusion**을 사용
                    - heat diffusion에 대한 설명 토글 (blurring과 관련)
                        
                        [Computer Vision, Machine & Deep Learning Blog](https://sunshower76.github.io/cv(computervision)/2020/03/23/Laplacian-of-Gaussian(LoG)-&-Difference-of-Gaussian(DoG)/)
                        
                        - heat diffusion 방식은 DoG (LoG에 대해 근사) 할때 사용
                            - 이미지의 causality
                                - 이미지의 전반적인 특징을 유지할 수 있다 (새로운 특징이 나타나지 않는다)
                            - causality 를 만족하면 linear heat diffusion equation과 같아짐
        - globally
            - **Lempitsky and Ivanov [15] 의 방법** (이 논문의 seam leveling 기반이 되는 방법)
                - two intuitive constraints에 따라 추가되는 vertex luminances optimal luminance 보정 term을 계산
                    - intuitive constraints에 대한 언급은 없어서 논문을 봐야 알듯
                    - 계산 결과
                        - luminance differences 가 작아짐
                        - derivative of adjustment within a texture patch 가 작아짐
                - 이 방법의 장점은 중간 정도(meso)나 큰 정도(large-scale)의 luminance changes가 발생하지 않음
        
        ![](/assets/img/LetThereBeColor/Untitled%203.png)
        

---

3. Assumptions and Based Method

- Assumptions
    - MVS 방식의 SfM으로 scene geometry reconstruction이 이 논문의 기본 전제이기 때문에 아래와 같은 문제점들이 challenging 됨
        - Reconstructed 3D model의 문제점
            - 추정된 camera parameter는 정확하지 않을 수 있음
            - 재구성된 모델은 scene을 잘 나타내지 못할 수 있음
        - 모델 재구성에 사용된 input images의 문제점
            - strong illumination
            - strong exposure
            - scale differences
            - 재구성된 모델에 포함되어 있지 않는 occluders (e.g.) pedestrians
- Based Method
    - 수정된 [15] 방법 + $\alpha$ 알고리즘 = 제안된 논문
    - [15]의 Overview Pipeline
        1. 모든 이미지로부터 "the visibility of faces" 를 결정
        2. [15] pairwise Markov random field energy 공식을 사용해서
            
            각 mesh face $F_i$ 에 부착될 texture에 사용되는 view $l_i$ 를 할당할 labeling $l$ 을 계산
            
            [simple pairwise Markov random field energy 공식]
            
            ![](/assets/img/LetThereBeColor/Untitled%204.png)
            
            → Label의 quality는 face에 부착할 view의 quality와 두 개의 face로부터 만들어진 edge의 smooth quality로부터 결정됨 
            
            - 목표
                
                $\min(E(l)) = E(l)+graph\space cut+\alpha \space extensions$  ([3] 에서 제안)
                
            
            - $E_{data}$
                - prefers "good" views for texturing a face
                
                | 고려사항            | Lempitsky and Ivanov [15] 방법 | Allen[2] & Lumigraph [4] 방법 | Gal [9] 방법 |
                | ------------------- | :----------------------------: | :---------------------------: | :----------: |
                | View Proximity      |               No               |              Yes              |     Yes      |
                | Image Resolution    |               No               |              Yes              |     Yes      |
                | Out-of-focus (blur) |               No               |              No               |     Yes      |
                | Angle               |              Yes               |              Yes              |     Yes      |
                
                - Lempitsky and Ivanov [15] 방법
                    - $E(l)$ 에 사용한 pairwise Markov random field energy 공식을 사용
                    - 이미지의 Viewing direction과 Face normal의 angle 사용 (Lambertian model)
                        
                        ![](/assets/img/LetThereBeColor/Untitled%205.png)
                        
                - Allene [2] & Lumigraph [4] 방법
                    - face를 view에다가 사영시켜서 그 사영된 사이즈를 $E_{data}$로 사용
                    - Lumigraph [4] 의 view blending weights도 이 방식과 비슷
                    - Out-of-focus 고려 안해서 blurry texture 유발
                - Gal [9] 방법
                    - 사영된 face에 합쳐진 이미지의 gradient magnitude 사용
                    - $E_{data}$ 가 커지는 경우
                        - Projection area가 큰 경우
                            
                            (face와 view가 (1)가깝고 (2)high resolution (3)orthogonal)
                            
                        - Gradient magnitude가 큰 경우
                            
                            (in-focus images, 아웃포커스될 때 blurring → low-pass filter 효과)
                            
                    - Seam visibility를 최소화하기 위해 x, y축 각각 60pixels씩 이동 가능하게 함
                        
                         → 2개의 DoF가 추가됨 (x축 이동 & y축 이동)
                        
                        <aside>
                        ⚠️ 이 논문에서는 이 방법을 안썼는데 그 이유로 seam visibility만 고려되고 input data는 설명되지 않는다고 함. 
                          (input data 설명이 안되면 어느 이미지에서 texture를 가져왔는지 모르기 때문인듯???)
                        - Texture patch는 원래 이미지와 비교하여 offset을 가진다고 함
                        - DoF를 늘리는 것은 computational complexity 증가 (large-scale에서 쓰려면 optimization이 필요)
                        
                        </aside>
                        
            - $E_{smooth}$
                - Lemptisky & Ivanov의 방법 [15]
                    - Seam부분의 오른쪽과 왼쪽 texture의 차이를 $E_{smooth}$로 사용
                        
                        → camera가 정확히 register되어 있거나 texture가 잘 aligned 되어 있는 seam을 사용하면 됨
                        
                    - 문제점
                        - Seam error integrals의 계산 시간이 많음
                            - Large-scale에서 사용 불가능
                            - 미리 계산이 불가능 (너무 많은 수의 seam 조합)
                        - blurry texture
                            - blurry texture는 seam error가 적게 나타남
        3. Color Adjustment (이부분 이해가 잘 안감)
            - minimizes seam visibility
            
             [Input] Eq.1 을 minimize하여 얻은 Labeling
            
             (1)  각 mesh vertex가 1개의 texture patch에 종속되어 있는지 확인해야함
            
            - seam의 각 vertex는 2개의 vertices로 복제해서 각 vertex가 unique 색 $f_v$ 을 가지게 함 (before adjustment)
                
                ![](/assets/img/LetThereBeColor/Untitled%206.png)
                
                ![](/assets/img/LetThereBeColor/Untitled%207.png)
                
            
             (2)  다음 공식을 최소화하여 각 vertex의 추가적인 보정 $g_v$ 를 계산
            
            ![](/assets/img/LetThereBeColor/Untitled%208.png)
            
            - First term
                - seam의 왼쪽 adjusted color와 right의 차이가 거의 없게 만듬
            - Second term
                - adjustment differences를 최소화함 between adjacent vertices within the same texture patch.
                - 점진적으로 texture patch안에서 adjustments하도록 만듬
            
               (3) 모든 vertices의 optimal $g_v$ 를 찾은 다음에, barycentric coordinates를 사용하여 $g_v$를 둘러싼 vertices로 부터 각 texel의 보정값을      
            
                   interpolation함
            
        4. 그 보정값들을 input images에 추가함. 
        5. texture patches들은 texture atlas에 packing되고
        6. texture coordinates들은 그 vertices에 붙이게 됨

---

4. Large-Scale Texturing Approach

(1) Preprocessing

1. Occlusions 체크 전에 현재 보고있는 View에 대해서만 rendering할 face를 결정
    - 제한은 어떻게 하나요?
        - back face culling
            
            ![](/assets/img/LetThereBeColor/Untitled%209.png)
            
        - View frustum culling
            
            ![](/assets/img/LetThereBeColor/Untitled%2010.png)
            
        
2. 남아있는 face-view combinations를 위해 Eq.1의 data term을 미리 계산
    
    ![](/assets/img/LetThereBeColor/Untitled%204.png)
    
    - 남아있는 face-view combinations들의 사용처
        - optimization
        - remain constant
        - fit into memory (the table has O(#faces $\cdot$ #views) entries and is very sparse)

(2) View Selection

= Labeling 얻고 texture 붙이기 (View Selection 전체 과정 2~4 과정의 overview)

- 아래 식을 기반해서
    
    $\min(E(l)) = E(l)+graph\space cut+\alpha \space extensions$  ([3] 에서 제안)
    
    - base algorithm $E(l)$의 data의 Smoothness terms 를 교체하고
    - photo-consistency check를 가지고 data term을 augment 함
    
- 그래서 다음과 같은 결과가 나옴
    
    ![](/assets/img/LetThereBeColor/Untitled%2011.png)
    

1. Data Term ($E_{data}$) 얻기
    - Gal et al [9] 의 data term 방법 사용
        - $E_{data}=-\int_{\phi (F_i, l_i)} \| \nabla (I_{l_{i}}) \|_2 dp$  (gradient magnitude $\| \nabla (I_{l_{i}}) \|_2$ 사용)
            - Gradient magnitude 계산
                - Projected face $F_{i}$ 가 있는 이미지에다가 Sobel 필터 하고
                - $F_{i}의 \space projection \space \phi (F_i, l_i)$  가 있는 모든 픽셀의 gradient magnitude (normalized) 을 더한다음에 그 평균을 area에 곱해서 data term구함
                - 만약에 projection이 1 pixel 미만이라면
                    - 그 프로젝션 centroid의 gradient magnitude를 projection area에 곱해줌
                - data term이 작아지는 경우 ( == 그래디언트 크기가 커질때)
                    - projection area가 클때
                        - close, orthogonal images with a high resolution
                    - gradient magnitude가 클때
                        - in-focus 이미지
            
            <aside>
            ⚠️
            
            Gradient magnitude의 문제점
              **Gradient magnitude가 클 때,** (in-focus, 작으면 out-focus → blurry)
              in-focus로 찍는 도중 pedestrian(Occluder)이 앞을 지나가는 경우가 있음
              이 이미지를 가지고 모델 재구성을 한다고 해도 pedestrian이 모델링 되진 않음
              그래서 View check을 할 때 이 view를 걸러내야하는데 Gal의 방법 [9]은 그러지 못함
              그래서 아래와 같은 텍스처링이 나옴
            
            </aside>
            
            ![](/assets/img/LetThereBeColor/Untitled%2012.png)
            
            - Occluder가 background보다 더 큰 gradient magnitude를 가질 수 있다.
            - 그래서 additional step to ensure "photo-consistency of the texture" 가 필요함
            
2. Photo-Consistency Check
    - specific한 face를 만들기 위한 두 가지 assumption
        - the majority of views
            - see the correct color
        - a minority of views
            - see wrong colors (occluders, less correlated)
    - Reject inconsistent views
        - mean 또는 median colors 사용 (Sinha [19] & Grammatikopoulos [13] 의 방법) → Not Sufficient!
        - Modified mean shift 알고리즘 사용
            1. Compute the face projection's mean color $c_i$ for each view in which the face is visible
            2. Declare all views seeing the face as inliers
            3. Compute mean $\mu$ and covariance matrix $\Sigma$ of all inliers' mean color $c_i$
            4. Evaluate a multi-variate Gaussian function $\exp(- \frac 1 2 (c_i-\mu)^T\Sigma^{-1}(c_i-\mu))$ for each view in which the face is visible
            5. Clear the inlier list and insert all views whose function value is above a threshold ( we use 6*10^-3)
            6. 3~5과정을 10번 반복 or until all entries of $\Sigma$ drop below 10^-5, the inversion of $\Sigma$ becomes unstable, or the number of inliers drops below 4
        - Modified mean shift 알고리즘 정리
            - 어떤 view에서 보이는 face들의 프로젝션의 mean color 계산하고 inlier라고 칭함
            - 각 view마다 똑같이 구해서 multi-variate gaussian function 구함
                
                ![](/assets/img/LetThereBeColor/Untitled%2013.png)
                
            - 위처럼 어떤 산등성이가 만들어졌으면 값이 가우시안 수치가 작은 부분들(= 이미지에서 자주 출현하지 않는 요소들)을 제거
            - 10번만 반복 || 공분산이 10^-5 이하가 될때까지 반복 || inlier 개수가 4이하 일때까지 반복
        - 각 view에 대한 a list of photo-consistent views 이 output으로 나옴
            - output을 제외한 view의 data term에다가 penalty를 곱해서 선택에서 제외되도록 만듬
            - a view → (photo consistent views의 data term + penalty*the other data term)
            
            <aside>
            ⚠️
            
            median 사용 X, mean 쓰세요. 
            median은 
                - 작은 query sets에서 동작하지 않음
                - marginal median은 보통 query의 member가 아니므로 너무 많은 view를 제거하게 됨
            mean은 실제로
                - shifting 하지 않으면 초기 평균값이 종종 inlier의 평균에서 멀리 떨어져있기 때문에 실제로 작동하지 않음 (Section 5에서 다룸)
                - Sinha [19] 는 사용하지 않을 부분을 user에게 직접 마킹할 수 있게 해줌
            
            </aside>
            
3. Smoothness Term
    
    Texture patch간의 seam의 정도를 나타냄, seam이 덜 나타는 쪽으로 texture matching
    
    - Potts model을 기반으로 만듬
        - $E_{smooth}=[l_i\ne l_j]$ ([ ]는 Iverson bracket)
            - Iverson bracket (조건이 맞으면 1, 아니면 0)
                
                ![](/assets/img/LetThereBeColor/Untitled%2014.png)
                
        - compact patches without favoring distant views 를 선호하지 않고 계산속도가 엄청 빠름
            
            (원거리 뷰를 선호하지 않는 compact patches 라고 번역에 나오는데 "원거리 뷰"가 맞는지??)
            

- View Selection 결과물 (with visible luminance differences between patches)
    - 얻은 Labeling에 photo consistency를 고려한 Data term을 통해 texture 붙인 결과 (smooth term은 seam이 얼마나 잘보이는지 정량적으로 나타내기만 하는듯)
    
    ![](/assets/img/LetThereBeColor/Untitled%2015.png)
    

(3) Color Adjustment

위에 그림에 patches 간의 seam들이 여전히 많이 보임 (color discontinuities) 그래서 seam visibility를 최소화하는 단계임

- Global Adjustment - [15] 방법 개선
- Local Adjustment - Poisson editing [16] 사용
1. Global Adjustment
    - Lempitsky & Ivanov [15]의 color adjustment의 문제점
        
        ![](/assets/img/LetThereBeColor/Untitled%208.png)
        
        - 2개로 복제된 unique 색 $f_{v_{left}}$와 $f_{v_{right}}$는 single location에 대해서만 evaluation 가능
            
            (= seam에 인접한 vertex v의 projection (두 이미지로 projection))
            
            - (내 생각, 토글에 원래 번역있음) 한개의 3d point를 가리켜야하는 두개의 이미지의 어떤 2d point가 Reprojection error가 작더라도 같은 3D point 위치를 가리키지 않음
                
                쪼그만한 registration errors가 (항상) 있더라도, 두 이미지로 떨어진 projection (of vertex v)는 실제 오브젝트의 같은 spot으로 대응하지 않음
                
        - if 만약 두 이미지가 다른 스케일을 가짐
            - then 3d point가 다른 곳에 span 되서 나타날 수 있음  (잘못 이해했을 수도 있음)
                
                Also, if both images have a different scale the looked up pixels span a different foot print in 3D.
                
        - MVS 방식은 여러 스케일로 찍어 points나 footprints가 서로 다른 스케일에서 가져와서 이 방식을 사용할 수 없음
    - Color Lookup **Support Region**
        
         위에서 제시된 문제를 해결
        
        - looking up a vertex's color value at the vertex projection (scale 문제 해결)
        - 인접한 모든 seam edges를 따름 (single location 해결)
        
        ![](/assets/img/LetThereBeColor/Untitled%207.png)
        
        위의 figure를 통해 설명
        
        - Vertex $v_1$ 에 대해 Red patch 위에 있는 $v_1$ 의 보정된 color $f_{v_1,red}$  를 (blue도 똑같이 계산)
            - $\overline {v_0v_1}$ edge와 $\overline {v_1v_2}$ edge를 따라서 Red image 의 color samples들을 averaging 해서 evaluate할 것임
            - 각 edge에서 edge 길이의 두 배의 샘플을 픽셀단위로 drawing
            - averaging the samples 할 때, Fig.4 의 오른쪽 plot에 따라 weight를 줄 것임
                - edge length로 엣지들을 weighted averaging 함 (엣지 위에있는 각 vertex들을 선형적으로 나타내는건가?)
                
                (e.g.) $v_1$ 일때 sample weight는 1이고 $v_1$에서 점점 더 멀어질수록 선형적으로 감소함 (양쪽으로)
                
                - 이렇게 plot 하는 이유 (barycentric coordinates를 사용해서)
                    
                    Eq.2 를 최적한 한 후 계산된 보정(correction) $f_{v_1, red}$ 이 barycentric coordinates를 사용하여 texel에 적용됨
                    
                    (barycentric coordinates(무게 중심 좌표) : 0~1 사이 값을 가짐)
                    
                    - 무게중심 좌표를 통해 ray와 삼각형의 교차점을 구하는 방법 (정리 짱 잘되어있음)
                    
                    [광선과 삼각형 교차 ( Intersecting ray and triangle )](https://mycom333.blogspot.com/2013/10/intersecting-ray-and-triangle.html)
                    
                    - 직관적인 설명 (이게 더 잘된듯)
                    
                    [barycentric coordinate](https://gyuyoul-textcube.blogspot.com/2009/11/barycentric-coordinate.html)
                    
        - 그래서 아래 식이
            
            ![](/assets/img/LetThereBeColor/Untitled%208.png)
            
            Tikhonov regularization에 의해 이렇게 변함
            
            ![](/assets/img/LetThereBeColor/Untitled%2016.png)
            
            where,
            
            $A, \Gamma$ : sparse matrices containing $\pm1$ entries to pick the correct $g_{v_{left}}, g_{v_{right}},g_{v_{i}}\space and \space g_{v_{j}}$from $g$
            
            $f : stacked\space f_{v_{left}}-f_{v_{right}}$ (이 term 구하는게 중요!)
            
             왼쪽 항의 첫번 째 term은 residual 항이고 두번 째 term은 regularization 항이다. 
            
            f는 이미 View Selection을 통해 정해진 상수이므로 바꿀 수 없기때문에 알파, 감마, g를 optimizing 해야한다.
            
            그래서 저 내용을 Conjugate Gradient를 사용해 최적화 하는데 SGD 방식과 비슷함 (gradient 계산을 행렬의 quadratic form으로 함)
            
            $\Alpha, \Gamma$ 행렬을 코드에서 어떻게 구했는지 아래 내용 정리 @TODO 정리하기 
            
            ![](/assets/img/LetThereBeColor/Untitled%2017.png)
            
            ~~여기서 g는 알파, 감마 행렬을 포함하기 때문에 g의 크기를 최소화하는 것이 seam을 줄이는 것과 같을 것이다.~~
            
            ~~Eq.2는 첫번째 term에서 g와 함께 알파, 감마행렬의 quadratic form으로 나타낼 수 있고 M=(알파행렬의 제곱 + 감마행렬의 제곱)은~~
            
            ~~sparse, symmetric and semi-definite 성질을 가지고 있다.~~
            
            ~~symmetric하고 semi-definite할 조건은  "각 고유값이 0보다 크거나 같다." 이므로 각 고유값이 엣지성분과 관련이 있다는 것을 알 수 있다.~~
            
            ~~그래서 M 행렬을 스펙트럼 분해를 해서 고유값 $\lambda_1,\lambda_2$ 를 얻을 수 있고 이 고유값들을 최소로 하면 flat한 성분을 얻을 수 있다. (harris-corner detector 참조)~~   ← 이거 아닌데 또 이렇게 생각할까봐 놔둠
            
            ![](/assets/img/LetThereBeColor/Untitled%2018.png)
            
2. Local Adjustment
    - Gal [9]의 방법을 수정
        - Poisson Editing
            - Poisson Editing
                
                [](https://www.cs.tau.ac.il/~dcor/Graphics/adv-slides/PoissonImageEditing06.pdf)
                
                Image는 foreground와 background의 weight-sum 으로 이루어짐
                
                $I(x,y)=\alpha(Foreground(x,y))+(1-\alpha)(Background(x,y))$
                
                where $\alpha$ = 0 or 1
                
                fuzzy the edge → color difference still remains
                
                - Multi-resolution blending ( [2] 인듯?)
                    
                    blend low-frequency changes across wide transition regions
                    
                    & belnd high-frequency changes across narrow transition regions. 
                    
                    image pyramid 방법, 이미지를 줄이면서 점점 wide kernel 적용하는 원리
                    
                    - 한계
                        
                        src Image와 Transition Image의 색이 너무 다르면 blending이 안됨
                        
                    - 아이디어
                        1. 결과 이미지는 edges는 반드시 src Image 색처럼 보여야하고
                        2. edges의 컬러는 (boundary)는 반드시 transition image와 일치해야한다.
                        
                        ![](/assets/img/LetThereBeColor/Untitled%2019.png)
                        
                    
            - Gal의 방법은 모든 texture patches에 진행을 해서 computational cost가 많이 듬
            - 이 논문에서는 the poisson editing of a patch를 20 pixel wide border strop 만큼 줌
                
                ![](/assets/img/LetThereBeColor/Untitled%2020.png)
                
                - Poisson equation boundary conditions : outer and inner rim
                    - Outer rim pixel's color = the mean of the pixel's color in the image assigned to the patch & the neighboring patch.
                    - Inner rim pixel's color = current color로 fixed (그냥 그대로 감)
                    - Patch가 너무 작은 경우에 inner rim만 omit함
                - The Poisson equation's guidance field = the strip's Laplacian.
            - 모든 patches를 LU분해를 한번만 하고 각 color channel에 사용 (YCbCr)
            - strips만 adjusting의 효율 >>> 모든 patch들을 adjusting의 효율 (시간, 메모리 측면에서)
            - 아래 방법 (양쪽 patch의 mean을 사용해 smoothing 하는 것)보다 더 weak한 form임 (이전에 global adjustment해서 저정도까진 필요 없다고 판단함)
                
                ![](/assets/img/LetThereBeColor/Untitled%2021.png)
                
            - Don't mix two images' Laplacians 해서 blending을 피함 (?????????) (지금까지 과정이 blending이 아니였어?)
            

---

- 이해가 잘 안가는 부분: Potts model, data term, poisson editing
    
    
    구멍들...
    
    - Data term
        - gradient magnitude 구하는 부분이 mapMAP에 있음,,,
        - Data term은 solver함수로 퉁치기때문에 내부를 알수가 없네
        - Photo-consistency check (mean shift 못봄)
    - smoothness term
        - potts model
    - View에서 texture 패치를 어떻게 구성하는지?
    - 이미지 출력!
    
    - global adjustment
        - 어떻게 라벨안의 모든 컬러가 변했는지?
    - local adjustment
        - 포아송 에디팅 (미적분책 다시보기 ㅠ)

---

- Graph (model)
    - 각 vertex마다 "인접한 faces"를 자료구조에 넣음
    
    <**코드>**
    
- texrecon.cpp (main)
    - generate_texture_views
        - 이미지가 어떤형식으로 저장되있냐 따라 불러오는 방식
        - texture_views == undistorted images
    - calculate_data_costs 각 face마다 candidate view를 설정하고 view에서
        - data cost는 뷰마다 있는 face들의 quality라고함
            
            data_costs->set_value(i, info.view_id, data_cost)
            
            (col, row, value)
            
            i=face #
            
        - quality는 각 face안에 있는 gradient magnitude의 합으로 정해지는데 gal[9]의 방법을 사용
        - calculate_face_projection_infos
            
            각 face마다 그에 해당되는 view, quality 그리고 mean color (YCbCr로 변환)
            
            - **view의 gradient magnitude 계산**
                - 각 뷰마다 각 face의 2d point를 구함
                - 프로젝션된 face의 area계산
                    - area가 너무 적으면 quality=0
                    
            - **Preprocessing**
                - backface
                    - face_to_view_vec.dot(face_normal)
                        - < 0.0f ->  face normal이 반대편인지?
                        - >75도  -> 정면에서 바라봣을때 사물이 75도이상 회전되어있음 잘 안보인다
                    - viewing_direction.dot(view_to_face_vec) < 0.0f // 내 눈 뒷편에 있는지?
                - view frustum
                    - texture_view->inside(v1, v2, v3)
                - occluder에 의해 사물이 눈에 안보일 때
                    
                    ```cpp
                    BVHTree::Hit hit;
                    if (bvh_tree.intersect(ray, &hit)) {
                        visible = false;
                        break;
                    }
                    ```
                    
        - Post process
            - photometric_outlier_detection (**Photo_consistency Check**)
                - 대다수의 view와 color가 많이 다른 view를 outlier라 놓고 제거
            - 각 face에 해당하는 여러 뷰들의 모든 value(quality)를 설정
                - quality는 퍼센트로 정함.
    - **View_selection**
        
        =각 face들마다 어느 view에서 가져온지 label을 찾음
        
         , crop한 부분의 mean color, quality에 대한 view list를 만들고  (preprocessing)
        
          그 view들중에서 high resolution, 정면 바라보는것 고름
        
        - construct graph
            - 그래프의 각 노드 (vertex_ 별로 인접그래프 연결후 edge 생성)
        - Pairwise <labelset, cost> // 한 face에 대해 <view 집합, view에 해당하는 cost집합>
            - LabelSet
                
                 data_costs_for_node == i번째 face에 해당하는 모든 view들의 pair<id, 퀄리티> == data_costs.col(i)
                
                각 face에 대해서 labels 정해줌
                
                label_set = 각 face에 대한 view들의 번호
                
                - face에 view가 없으면 0
            - cost
                
                unaries = cost 저장 (face에 view가 없는 경우 1)
                
        - **labeling**
            
            각 face마다 label # (== view #)을 지정해줌 그래서 (1 view ↔ 1 face) 
            
            - graph→set_label(face#,  label_set.label_from_offset(face#, solution[face#])
        
        [**//TODO](//todo) 어떻게 최적의 뷰를 찾아내는지 smooth term 과 함께 찾기**
        
    
    - generate_texture_patches
        - 각 texture view마다 하나의 label이 있고 label마다 여러개의 face set (subgraph)들이 있음
        - generate_candidate
            - label마다 하나하나의 subgraph를 for문 돌려서 각 subgraph마다 하나의 candidate를 생성
                - candidate = texture_patch 인데 texture_patch는 subgraph를 뷰에 사영시켜서 좌표를 얻음
                - texcoords = vertex를 사영시킨 좌표
                - 여기에 bounding box그려서 cropping해서 rect 형태로 candidate 저장
                - candidate중에서 겹치는 부분이 있으면 제거 (bounding box 안에 있는지 확인)
                - 나머지는 vertex_projection_infos에 (projection 정보, texture patch #, face_id) 저장
        - fill hole (Isotropic Surface Remeshing, Fuhrmann 논문 기반)
            - subgraph (face set) 에서 vertex 중간부분을 메꾸는거인듯?
            - face 개수가 100개가 넘어가는 subgraph는 안함
            - 인접한 face를 한바퀴 돌았을 때 자기자신이 아닌경우 hole 발생
            - hole을 매꿨을 때 unseen_face가 발생하므로 여기서 다시
            - fill hole과 아닐때와 비교 (fill hole을 하면 patch가 더 생김 & patch자체 경계부분 매꿔짐)
                
                no_fill_hole 결과 → fill_hole 결과1, border쪽에 매꿔짐
                
                ![](/assets/img/LetThereBeColor/Untitled%2022.png)
                
                fill_hole 결과2, 저렇게 border 매꿔줘서 합쳐진지 모르겠는데 새로운 patch도 생성됐음
                
                ![](/assets/img/LetThereBeColor/Untitled%2023.png)
                
            - unseen_face를 candidate처럼 vertex_projection_infos에 저장
    - seam_leveling
        - global adjustment
            
               각 texture patch에 있는 모든 vertex color정보를 넣은 다음에
            
            - texture_patch→adjust_colors();
                - face의 bounding box를 구함
                - face의 barycentric coords를 구해서 box안의 각 texel마다 interpolated color값 입력
                
                <aside>
                💡 만약 무게중심이 이미지 밖을 벗어나면 픽셀의 거리가 1 픽셀 이상인지 알아보기
                
                </aside>
                
                ![왼쪽: 바깥쪽. 무게중심좌표중 하나가 0이하인 곳에서 있으면 blending 할때와 안할때 차이](/assets/img/LetThereBeColor/Untitled%2024.png)
                
                왼쪽: 바깥쪽. 무게중심좌표중 하나가 0이하인 곳에서 있으면 blending 할때와 안할때 차이
                
        - local adjustment
            - seam edge 구하고 그것의 projection 계산
            - edge 정보 가지고 길이 계산해서 color sampling
                - 가장 긴 edge의 길이를 기준으로
                - for 모든 엣지 정보를 차례대로
                    - 가장 긴 엣지의 길이 *2 만큼 선형적으로 샘플링해줌
                        - 0~1까지 1 / 가장 긴 엣지*2 의 샘플 개수만큼 나눠줌
                        - mean_color_of_edge_point는 한개의 seam에 대해서 컬러 샘플링
                            - seam이 여러 vertex로 연결되어 있으니까 각 edge마다 반복수행
                                - 각 샘플마다 각 픽셀은 양쪽 vertex의 1차 보간
                                - **-1-2-3-4-5-**1-2-3-4-5-* 라고 하면
                                    - 샘플된 컬러 2끼리 모두 더해서 그거의 mean_color 사용
                                
            - poisson editing
    - Texture Atlas 생성

---

- 전체 과정
    - 패치로 오려낸 직후 (global adjust 이전)
        
        ![](/assets/img/LetThereBeColor/test280.png)
        
    - 해당 view에 vertex를 사영함 (texture patch 생성 코드에서 fill hole 할때 remesh한거 같은데 코드 이해를 못함)
        
        ![](/assets/img/LetThereBeColor/Untitled%2025.png)
        
    
    - 블렌딩할 영역 선택
        
        각 삼각형마다 bounding box를 만든다음에 0,0부터 n,n까지 scan하면서 
        
        - 흰 부분
            
            color adjustment할 부분, 스캔하는 texel이 삼각형 안에 있으면 유효한 픽셀 (validity_mask)로 함
            
        - 회색 부분
            
            스캔하는 texel이 삼각형 밖에 있는 것들중에서 삼각형으로부터 거리가 1픽셀차이나는 애들은 회색으로 만듬 (blending_mask=64) 이 애들로 poisson editing할 경계선으로 만들어줌
            
        - 검은 부분
            
            label이 존재하지 않는 곳, 스캔했을 때 흰, 회색 영역에 들어가지 않는 나머지들은 0으로 만들어서 버림
            
        
        ![](/assets/img/LetThereBeColor/Untitled%2026.png)
        
        ![](/assets/img/LetThereBeColor/Untitled%2027.png)
        
        - 영역을 분리한 다음에 검은 부분 제외하고 최적화& vertex 보간을 통해 얻은 adjust_values들을 원래 픽셀에다가 더해줌
    - Global seam leveling 직후
        
        ![](/assets/img/LetThereBeColor/test286.png)
        
    
    - poisson editing 직후 (별 차이는 안보임)
        
        ![](/assets/img/LetThereBeColor/test255.png)
        
    
    - 경계 주변으로 margin 설정 (가우스)
        
        
        ![](/assets/img/LetThereBeColor/Untitled%2028.png)
