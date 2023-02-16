# Keras CNN을 이용한 고양이와 개 이미지 분류기

## 1. data preprocessing

### 기능소개
* Image Generator 및 데이터 증강을 만드는 기능을 수행
* 이미지 로드, 크기 조정, 데이터 분할

### 작업내용
* ImageDataGenerator 객체 생성, ‘rescale’ 인수를 통해 이미지 데이터의 스케일 조정
* ‘Flow_from_directory()’ 함수를 사용하여 디렉토리에서 이미지 로드, ‘target_size’ 인수를 통해 이미지 크기 조정
* ‘batch_size’ 인수를 통해 생성할 배치 크기 지정, ‘class_mode’ 인수를 통해 이진 분류 문제임을 명시
* ‘shuffle’ 인수를 통해 데이터를 섞을지 여부를 지정, ‘subset’ 인수를 통해 분할된 데이터의 일부를 선택
* ‘train_gen’, ‘val_gen’ 두 개의 generator를 생성하여 학습데이터와 검증 데이터를 생성함.  

![image](https://user-images.githubusercontent.com/119937883/219278932-36b431d7-9c87-4414-bf09-58f9f4098596.png)



## 2. build model

### 기능소개
* Keras를 사용하여 CNN을 구성하는 build_model 작성

### 작업내용
* 150x150 크기의 3채널의 이미지를 입력 받는다.
* 4개의 Conv2D 층은 각각 32, 64, 128, 256개의 필터를 사용하여 입력 이미지에서 특징을 추출한다.
* 필터의 크기는 (3,3)이며, 출력 활성화 함수는 relu이다.
* padding=‘same’ 옵션으로 출력 크기가 입력과 동일하도록 설정한다.
* MaxPooling2D 층은 입력 크기를 절반으로 줄이는 풀링 연산을 수행한다.
* Flatten층을 사용하여 4차원 텐서를 1차원으로 변환 후 2개의 Dense 층을 거쳐 이진 분류 결과를 출력한다.(이전 층의 추출된 특성을 사용하여 더 복잡한 패턴 학습)
* 출력층의 활성화 함수는 sigmoid 로 한다.  

![image](https://user-images.githubusercontent.com/119937883/219279257-2e0462b6-a31b-4ce5-a330-6b4b99bde287.png)



## 3. process

### Target_size, batch_size 설정 변경
* 기존 target_size=(64,64) > 변경 target size=(150,150)  
  기존 target_size로 작업시 해상도의 문제로 정확도가 떨어질 수 있겠다 생각되어 좀 더 높은 해상도의 모델로 변경 하여 좀 더 정확한 featur를 추출하였다.
* 기존 batch_size=32 > 변경 batch_size=100  
  기존 batch_size보다 더 다양한 패턴으로 학습
* 하이퍼 파라미터는 적정값으로 선택하는 것이 중요하고 큰 이미지로 인해 처리속도 저하 및 메모리 사용량 증가로 GPU 메모리가 부족할 수 있다는 사실 확인.

### Optimizer 변경
* 기존 SGD optimizer에서 Adam optimizer로 변경하였다.
* SGD 사용시 그래프가 지그재그로 수렴하여 정확도 측면에서 불안정하였고, 또한 비교적 적은 데이터셋이라 생각되어 조금 더 안정적이고 높은 정확도를 얻을 수 있는 Adam을 사용하였다.

### 데이터 증강
* 꽤나 많은 옵션으로 학습을 시키려고 하였으나 오히려 정확도가 떨어지는 결과를 초래하엿다.
* 모델이 지나치게 복잡해져 과적합(overfitting)이 발생되었다 생각한다.  

![image](https://user-images.githubusercontent.com/119937883/219279825-e5ad7230-3789-4641-a1aa-b45e9a0f1441.png)


### 모델 변경
* Dense 모델에서 Conv2D 모델로 변경
* 정확도 상승이 되지 않았고 노드의 수가 적어서 그럴 수 있다는 생각이 들어 이미지와 같은 2D 데이터에 좀 더 높은 정확도를 보이는 Conv2D 모델로 변경하였음.  

![image](https://user-images.githubusercontent.com/119937883/219279897-5939b62c-a1a4-452f-9cfa-82a157bda8bf.png)  

![image](https://user-images.githubusercontent.com/119937883/219279918-006dc715-bd7a-41b1-a2fc-9b807007637b.png)



## 4. results
### Adam optimizer
* 현재의 정확도에 다가갈 수 있었던 결정적인 요인은 optimizer의 변경이었다. SGD 사용시 약 75%의 성능에서 데이터 증강 및 모델 변경에 있어서도 만족할 만한 정확도를 얻지 못하였다.
* 이 모델은 GPU로 약 10분 정도의 훈련 후 데이터셋에서 약 96%의 정확도를 보였고, 개와 고양이 이미지를 분류하는 데 있어서는 상당히 높은 성능을 확인할 수 있었다.  

#### SGD optimizer result
![image](https://user-images.githubusercontent.com/119937883/219280217-5928f691-3983-46ef-8167-983ce8b697d6.png) 

#### Adam optimizer result
![image](https://user-images.githubusercontent.com/119937883/219280251-cc4859ee-f461-4755-9cc7-d21fa5f79afc.png)  






