---
layout: post
title: Tensorflow Note
comments: true
categories : [Others]
tags: [tensorflow]
---
**2020/09/08**    
-----------
- 32X32 이미지를 256X256 으로 resize하기 작은 작전
    1. 32X32에서 256X256 으로 resize 하는 방법은 그냥 tf.image.resize하면 된다고 생각했다.
    2. 근데 막상 1만장을 tf에 적재할때 resize하니까 OOM 발생
    3. 그래서 독립적으로 실행하는 모듈을 만들어줌. 성공!    
    
- Large scale datasets을 tf api에 적재하기 대작전
    1. tf.data.Dataset.from_tensor_slices : 50,000개 이미지 256x256 이미지 넣으면 OOM 발생, 32x32로 축소
    2. 256x256 image를 적재하기 대작전
        - 그래픽카드 메모리 사용량을 제한 (실패 - 프로세스 터짐)    
            ```python
            gpus = tf.config.experimental.list_physical_devices('GPU')    
                    if gpus:    
                        try:    
                            tf.config.experimental.set_virtual_device_configuration(gpus[0], [tf.config.experimental.VirtualDeviceConfiguration(memory_limit=4096)])   
                        except RuntimeError as e:    
                            print(e)    
            with tf.device('/gpu:1')
                train_ds = tf.data.Dataset.from_tensor_slices((train_images, train_labels))
                test_ds = tf.data.Dataset.from_tensor_slices((test_images, test_labels))
            ```    
        - dataset을 여러개의 tf.data.dataset.from_tensor_slices()로 나눈 다음에 session에 돌려보기 (실패- 프로세스 터짐)    
        
        - 256x256 사이즈 데이터셋 저장 후 tfrecord로 변환해보기
          - 진행중인 상황: label:int64, image:int64 인데 bytes로 변환하고 싶다............. image는 255 나누면 자꾸 빠갈나서 (256x256 일때만) 돌리기가 싫ㅎㅎㅎ다 ㅎㅎㅎ
          - 지금 image는 ndarray 형인데 int형을 요구함 . 이거 해결하기 위해서 오른쪽 창에 image to tfrecord 띄어놈 
     
--------------------
**2020/09/11**
- Custom Optimizer 만들기 대작전
    1. keras natvie library안에 들어가서 SGD+momentum에 weight_decay 변수만 추가하려고 했는데  실질적으로 연산 코드는 c++에 작성되어 있다.
    그래서 이 방법은 쓰지 못할듯 ㅠㅠ    
    2. resource_apply_dense 함수를 업데이트 하고 싶은데.... tf.assign() 함수가 V2로 넘어오면서 deprecated 되었다. 근데 이걸 대체할 만한 것들이 안보임!!
    3. 혹시나 해서 resource_apply_dense 안을 비워봤다. 근데 업데이트가 된다 ㅋㅋㅋㅋㅋㅋㅋ 후........... 이 의미는 loss update가 전혀 안된다는 것이다.