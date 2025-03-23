---
title: Tensorflow Note
comments: true
tags: [tensorflow]
---
-------
**2020/09/08**    
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
    4. tfrecord 변환 끝나면 이제 함 실험해볼라고

--------
**2020/09/21**

- imagenet dataset 을 tfrecord로 변환하기 대작전
    1. 내가 가진 자원, 16기가 메모리, 를 가지고 데이터셋을 tf에 한번에 적재하기!    
    2. 왜냐하면 나눠서 적재하는거... 머리속에 안그려지기도 하고 bin 파일로 만들어서 한번에 메모리에 적재하면 빠르기도 하고!    
    3. 하지만 imagenet 데이터셋은 ~138GB, 그래서 나온게 tfrecord    
    4. alexnet 만든 사람의 cifar 10 bin 데이터 6만장은 160mb 까지 나왓는데 (물론 32x32 reshape 해줘야 하는 작업이 더해짐)    
    5. 이거는 tf가 알수 없는 bin 파일임. 그래서 tf 적재전에 decoding해서 적재해줘야함 -> 무쓸모     
    6. 그래서 나는 tfrecord로 처음시도햇을때 30GB이상 나옴. 자원 초과함.    
    7. 그래서 이미지 퀄리티를 70으로 줄이면 ~13GB 까지 된다는 자료가 있어 지금 그걸로 시도중 jpeg 압축 이미지 퀄리티를 줄여도 모델훈련에는 문제 없음
        - cifar 데이터로 tfrecord 만드는 작업을 테스트 했을때 encoding 하는 과정에서 이미지 bin 데이터를 tf.train.bytelist에다가  set_sequential_values로 넣어줬음
        - 이걸로 decoding도 가능했었는데 그 이유는 이미지 데이터를     
            ```
            with open(file_path, "rb") as f:
                f.read()
            ```    
            자체로 tfrecord로 변환 해주니까 된거였음.
        - 이번에는 이미지 resizing + bin encoding 을 한번에 해야하는 작업이라 이미지 디코딩 -> resizing -> encoding -> tfrecord 해줘야 했는데
        - 자꾸 tfrecord 받아오는 데이터가 못 읽는 것이였음.
        - 문제를 찾다 보니 tf.train.bytelist를 그냥 argument값인 value를 그대로 옮겨다가 넣어줬음 된거임....
        - 조심하자 ㅠㅠ 5일동안 이유를 못찾았는데 겨우 해결하고 파싱중... 근데 4일걸린다... 8 core 모두 사용해도...

**2020/09/26**

- loss가 Nan이 뜨던 원인을 찾았다. 그건 바로 **tf.image.convert_image_dtype** 이다.
    - 나는 데이터형변환만 필요했는데 저 함수는 **scaling** 까지 해버린다.
    - 그래서 imagenet mean을 빼주면 전부다 imagenet mean에 맞춰지니까 당연히 Nan이 뜨지 ㅠㅠㅠㅠㅠㅠㅠㅠㅠㅠ
    - 아 진짜 증오한다ㅏ아아아아아아아ㅓㄹ머ㅗㄴ아ㅓ론마이뢈ㅇ니

- loss Nan이 뜨던 원인2
    - sparse categorical crossentropy는 ~~~! 인덱스 0부터임... ㅠㅠ
