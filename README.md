# powersoh
EDA, 모델링, 결과 확인 과정의 ipynb 파일
</br>
추후 보완 할 예정

# 프로젝트 배경과 목적
AI 분야 중 스마트그리드·스마트팩토리 4차 산업혁명의 핵심인 개별 설비·장비별 에너지 효율화·에너지 사용 패턴 분석·설비별 이상 감지 등 AI 핵심 알고리즘 개발 필요
그 중 설비별 이상 감지를 위한 분류모델을 만들고자 함

# 프로젝트 진행 과정
1. AI허브에서 데이터 수집
2. 데이터 파악
3. EDA
4. 모델링
5. 결과 확인

# 데이터셋 설명
train, test 데이터셋이 존재

Input : 일정 시간동안 관측한 20 여개의 에너지 사용 패턴 관련 feature

Output : ' 역률 ', ‘전류고조파 ', ‘전압고조파 ' 3 가지 도메인에 대한 SOH 분류 정상 :0, 주의 :1, 경고 :2
               test 데이터셋은 output이 없음

  ＊feature 관련 표 (역률, 전압고조파, 전류고조파는 평균만 있음)
  ![image](https://user-images.githubusercontent.com/97713997/193463486-d2944aa4-fbe2-49b5-81ee-c455ffaffa8a.png)
  
# 문제 상황 해결 과정
분류해야 될 타켓이 3가지이기 때문에 각 타겟에 대한 데이터셋 3개를 만들었습니다.

각 데이터셋에 대해 EDA를 진행하였습니다.

역률의 경우는 가지고 있는 feature들로 전체의 역률, 각 RST상의 역률을 나타내는 데이터셋으로 만들었습니다.

전류,전압 고조파의 경우는 제가 알기론 정현파에서 fft으로 고조파 성분을 확인하는 것이었는데 주어진 데이터는 데이터 간 시간 차가 크기 때문에 정현파를 알 수 가 없어서 고조파에 어느 정도 영향이 있을 것 같은 feature을 선택하는 방법을 이용했습니다.

분류 문제이기 때문에 최빈값으로 베이스모델을 만들었고, 역률은 로지스틱 회귀 모델, 전류고조파는 랜덤포레스트 모델, 전압고조파 xgboost 모델을 사용하였습니다.

원래는 한 타겟에 3가지 모델을 써보고 비교해보려고 했으나 시간상 각 1개씩 모델을 적용하게 되었습니다.

평가지표는 f1과 auc를 사용했습니다.

# 결과 정리
### 1. 역률
 ＊베이스모델 - 최빈값
</br>
![image](https://user-images.githubusercontent.com/97713997/193464381-815cf025-f552-43df-85c7-74526d6f0fc3.png)
</br>
 ＊로지스틱회귀
</br>
![image](https://user-images.githubusercontent.com/97713997/193463666-7ed8f305-5ff7-4727-b6df-412c13349ed6.png)
</br>
### 2. 전류고조파
 ＊베이스모델 - 최빈값
</br>
![image](https://user-images.githubusercontent.com/97713997/193463707-d936f308-f97d-4528-92b9-b9fdfdcff209.png)
</br>
 ＊랜덤포레스트
</br>
![image](https://user-images.githubusercontent.com/97713997/193463727-e23778a4-60b9-40f7-b143-4591e4b0bf0b.png)
</br>
### 3. 전압고조파
 ＊베이스모델 - 최빈값
</br>
![image](https://user-images.githubusercontent.com/97713997/193463750-78d3a21f-fd80-4e49-b8a3-70f780d5af45.png)
</br>
 ＊xgboost
</br>
![image](https://user-images.githubusercontent.com/97713997/193463785-25208f00-879e-456e-a4a3-f9283890535e.png)
</br>
</br>
ai허브에서는 평가지표는 macro f1이였고 제출 결과는 밑의 이미지입니다.
</br>
![image](https://user-images.githubusercontent.com/97713997/193463843-0008af29-90cf-4fd7-a343-8361335ebf39.png)
</br>
</br>

# 한계점 및 해결 방안
NaN 값 처리를 간단하게 각 열의 평균값으로 했는데, 좀 더 나은 NaN값 처리를 하게 된다면 모델 성능을 좀 더 높일 수 있을 것 같습니다.

전압고조파 결과가 다른 타겟에 비해 떨어지는데 feature 선택에 대해 좀 더 생각해봐야 할 것 같습니다.

주어진 train 데이터셋을 train과 val로 나눴지만 test 데이터셋에 타겟값이 없어서 일반화가 됬는지 확인을 못했는데, 지금 생각해보면 train 데이터셋을 train, val, test로 나누고 이 때 생긴 test로 일반화가 됬는지 확인하면 될 것 같습니다.

각 타겟의 모델을 통일하고 모델의 수를 늘리고, 베이스 모델과 만든 모델의 평가지표도 통일하면 좀 더 논리적일 것 같습니다.
