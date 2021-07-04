# deep-clssifier-alexNet

# 첫 classifer논문 기록: AlexNet

# 들어가기 앞서 부족한 내용 정리

# PCA를 정리해보자!
## 공분산이란? => 
### 데이터 구조적의미: feature들간의 변동이 얼마나 닮았나?
![image](https://user-images.githubusercontent.com/61177857/124377817-fff98c80-dce8-11eb-9e06-6bd883804332.png)
![image](https://user-images.githubusercontent.com/61177857/124377859-418a3780-dce9-11eb-87ff-3d7281de6036.png)
![image](https://user-images.githubusercontent.com/61177857/124377981-f58bc280-dce9-11eb-8316-0349bd2b570d.png)
참고로 샘플수 n으로 나누어줄때, n이 아닌 n-1로 나누어주는데
이는 모집단이 아닌 표본집단에서 샘플을 추출하였기 때문이다.

## 그럼 공분산을 통해 PCA 어떻게 하지?

피처가 두개일때를 생각해보면
샘플들이 피처의 두 축으로 아래와 같이 들어가게 된다.

![image](https://user-images.githubusercontent.com/61177857/124379369-874afe00-dcf1-11eb-93ef-68f4a23446ef.png)

그리고 2-dim 에서 1-dim으로 축으로 projection된것을 보면(오른쪽)
위에서 아래그림으로 내려갈 수록 분산이 작아짐을 확인할 수 있다.
이떄의 분산은 위에서 우리가 구해준 공분산의 eigen value와 비례한다.

따라서 만약 5-dim의 피처들(5개의 피처들)로 공분산을 구했다면,
해당 공분산의
A(공분산,A)x = (eigen_val)x로 eigen value를 구하고, 그에 대응하는 x(eigen_vector)를 구한다.

그리고 해당 5-dim의 피처들을 2-dim 피처로 줄이고 싶다면,
가장 큰eigen value 상위값 두개를 선택하고 해당 eigen vector로 정사영시켜주면
2-dim으로 차원축소가 가능하다!
이것이 바로 PCA다 

=======================================================================================================================

# AlexNet

## AlexNet을 크게 요약하자면 다음과 같다.
### 1.Data augmentaion : RGB on PCA , Crop
### 2.acivation Ftn : Relu => (LRN)
### 3.Multi GPU
### 4.Overlab pooling
### 5.drop out

## 1.Data augmentaion : RGB on PCA , Crop
### RGB on PCA
RGB의 공분산 (3*3)을 통해 3개의 eigen value를 구하고,
해당 eigen val의 eigen vector로 projection해준다.

예시)
![image](https://user-images.githubusercontent.com/61177857/124379980-4359f800-dcf5-11eb-9e39-29329c4bf6c1.png)

그러면 데이터를 늘려 overfiting되는 것을 막을 수 있다.

### CROP
이미지 하나당, 10개의 이미지로 Augmentation해주는 기술

![image](https://user-images.githubusercontent.com/61177857/124380086-d09d4c80-dcf5-11eb-8e55-502bd1917b5a.png)

위의 그림과 같이 256*256이미지를 
중앙, 오른쪽 위, 오른쪽 아래, 왼쪽 위, 왼쪽 아래로 224*224로 crop해준다

역시 data 크기를 늘려 overfiting을 

### 2.acivation Ftn : Relu => (LRN)
Alex Net이전에는 acrtan, sigmoid와 같은 seturating non linear Ftn을 사용함
하지만, 이는 커널을 통과한 값들이 양끝 값으로 몰렸을 때, gradient vanising문제가 발생한다.
이때 relu로 non seterating non liearal Ftn을 사용해서 해당 문제를 해결했다.

그러나 relu의 경우 0이상인 x가 한없이 커지는 방향으로 가므로,
커널과 activate ftn을 통과한 데이터들이 중간중간 주위보다 매우 큰값을 가지게 되는 데
이때 아래 그림과 같은

![image](https://user-images.githubusercontent.com/61177857/124380433-874dfc80-dcf7-11eb-9873-335cb6d4cad8.png)

측면억제가 일어난다. (측면억제 : 큰값이 작은 값에도 영향을 줌)

그러면 학습이 제대로 일어나지 않기때문에 이를 막기위해 
Local Respond Nomalization을 해주었다.


### 3.Multi GPU

그때 당시 120만개의 데이터로 GPU에서 딥러닝을 돌리기에는
용량이 부족해서 2대의 GPU로 네트워크를 나눠주어 병렬처리로 돌렸다.

### 4.Overlab pooling

위 논문에서는 pooing 3*3에 stride 2를 해줌으로써
overlab pooling을 했고, 이때 성능이 우수했다고 한다.

### 5.drop out

데이터를 돌려줄때 fc layer에 드롭아웃을 해서 overfiting되는 것을 막았다.


## AlexNet architecture

![image](https://user-images.githubusercontent.com/61177857/124380624-9da88800-dcf8-11eb-8c58-c5c6dbc9b14e.png)






