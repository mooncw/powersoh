# powersoh
EDA, 모델링, 결과 확인 과정의 ipynb 파일 (2022.10 ~ 2022.10)

# 프로젝트 배경과 목적
AI 분야 중 스마트그리드·스마트팩토리 4차 산업혁명의 핵심인 개별 설비·장비별 에너지 효율화·에너지 사용 패턴 분석·설비별 이상 감지 등 AI 핵심 알고리즘 개발이 필요합니다.

그 중 설비별 이상 감지를 위한 분류모델을 만들면 전력설비의 효율성 유지가 용이해지고 점검 시점을 파악하기 쉬워질 것이라 생각했습니다.

# 프로젝트 진행 과정
1. AI허브에서 데이터 수집
2. 데이터 파악
3. EDA
4. 모델링
5. 결과 확인

# 데이터셋 설명
train, test 데이터셋이 존재

Input : 일정 시간동안 관측한 20 여개의 에너지 사용 패턴 관련 feature

Output : '역률', '전류고조파', '전압고조파' 3 가지 도메인에 대한 SOH 분류 정상 :0, 주의 :1, 경고 :2
</br>
(test 데이터셋은 output이 없음)

###  **※ feature 관련 표 (역률, 전압고조파, 전류고조파는 평균만 있음)**
  ![image](https://user-images.githubusercontent.com/97713997/193463486-d2944aa4-fbe2-49b5-81ee-c455ffaffa8a.png)
  
# 문제 상황 해결 과정
분류해야 될 타켓이 3가지이기 때문에 각 타겟에 대한 데이터셋 3개를 만들었습니다.

각 데이터셋에 대해 EDA를 진행하였습니다.

역률의 경우는 가지고 있는 feature들로 전체의 역률, 각 RST상의 역률을 나타내는 feature을 만들었습니다.
추가로 feature을 선택하였습니다.

전류,전압 고조파의 경우는 제가 알기론 정현파에서 fft으로 고조파 성분을 확인하는 것이었는데 주어진 데이터는 데이터 간 시간 차가 크기 때문에 정현파를 알 수 가 없어서 고조파에 어느 정도 영향이 있을 것 같은 feature을 선택하였습니다.

분류 문제이기 때문에 최빈값으로 베이스모델을 만들었고, 각 label 별로 로지스틱 회귀 모델, 랜덤포레스트 분류 모델, xgboost 모델을 사용하였고 랜덤서치로 하이퍼 파라미터 튜닝을 진행 했습니다.

그중 가장 결과가 좋은 랜덤포레스트 분류 모델을 최종 모델로 선택했습니다.

평가지표는 accuracy와 macro_f1_score를 사용하였습니다.

# 결과 정리
### 1. 역률
 **베이스모델 - 최빈값**
</br>
![image](https://user-images.githubusercontent.com/97713997/195078475-56e986dd-7b76-4ac8-a7ef-f1479e597ee9.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195078577-26f399b5-f2d0-48b0-9ae2-b5a9582e6e91.png)
</br>
**macro_f1_score: 0.23**
</br>
</br>
</br>
 **랜덤포레스트**
</br>
![image](https://user-images.githubusercontent.com/97713997/195079333-5d3d8870-5a2c-422c-8f74-fbfd465ca41c.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195079452-4a78002f-b655-444a-a48e-5541963b5dcf.png)
</br>
**macro_f1_score: 0.94**
</br>
</br>
</br>
### 2. 전류고조파
 **베이스모델 - 최빈값**
</br>
![image](https://user-images.githubusercontent.com/97713997/195083276-e2a06696-d62f-447f-84e9-666fe43c78ec.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195083359-a71ce0f9-a52e-4ec6-b442-57bcac8db5c1.png)
</br>
**macro_f1_score: 0.30**
</br>
</br>
</br>
 **랜덤포레스트**
</br>
![image](https://user-images.githubusercontent.com/97713997/195083658-1c7a9972-52c4-4b0e-86f0-e79f2158cf5f.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195083693-2b6bc5df-cbfd-466b-bd84-266bb2d97424.png)
</br>
**macro_f1_score: 0.95**
</br>
</br>
</br>
### 3. 전압고조파
 **베이스모델 - 최빈값**
</br>
![image](https://user-images.githubusercontent.com/97713997/195083876-b2c95354-a461-4cbc-9f7f-a48a83f2d833.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195083927-d4a81f41-a448-4bfe-9862-cdc385d8c926.png)
</br>
**macro_f1_score: 0.19**
</br>
</br>
</br>
 **랜덤포레스트**
</br>
![image](https://user-images.githubusercontent.com/97713997/195084129-f4038ec5-6367-4aa4-a753-0e1fe138250f.png)
</br>
![image](https://user-images.githubusercontent.com/97713997/195084194-f5bc4a05-70a1-4b6f-a914-b5f9aca00cfd.png)
</br>
**macro_f1_score: 0.92**
</br>
</br>
</br>
### 4. 최종결과

ai허브에서는 평가지표는 macro_f1_score이고 제출 결과는 밑의 이미지입니다.
</br>
![image](https://user-images.githubusercontent.com/97713997/195084384-c67e417f-0f03-4e92-ac95-bd82d14245ec.png)

</br>
</br>

# 만족스러웠던 부분
여러 시도의 feature engineering으로 성능 개선을 했습니다.

# 한계점 및 해결 방안
최종결과가 기대한 만큼 결과가 나오지 않았습니다.

더 좋은 feature 엔지니어링 방법이 있다면 결과가 더 좋아질 것 같습니다.

랜덤포레스트 분류보다 좋은 모델을 만들면 결과가 더 좋아질 것 같습니다.
