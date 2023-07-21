# powersoh
EDA, 모델링, 결과 확인 과정의 ipynb 파일 (2022.10 ~ 2022.10)

## 프로젝트 배경과 목적
AI 분야 중 스마트그리드·스마트팩토리 4차 산업혁명의 핵심인 개별 설비·장비별 에너지 효율화·에너지 사용 패턴 분석·설비별 이상 감지 등 AI 핵심 알고리즘 개발이 필요합니다.

그 중 설비별 이상 감지를 위한 분류모델을 만들면 전력설비의 효율성 유지가 용이해지고 점검 시점을 파악하기 쉬워질 것이라 생각했습니다.

## 데이터셋 설명
train, test 데이터셋이 존재

Input : 일정 시간동안 관측한 20 여개의 에너지 사용 패턴 관련 feature

Output : '역률', '전류고조파', '전압고조파' 3 가지 도메인에 대한 SOH 분류 정상 :0, 주의 :1, 경고 :2
</br>
(test 데이터셋은 output이 없음)

###  **※ feature 관련 표 (역률, 전압고조파, 전류고조파는 평균만 있음)**
  ![image](https://user-images.githubusercontent.com/97713997/193463486-d2944aa4-fbe2-49b5-81ee-c455ffaffa8a.png)
  
## 진행 과정
### 1. AI허브에서 데이터 수집
### 2. EDA
**1)데이터에 대한 정보 파악**
* 데이터 크기

<img src="https://github.com/mooncw/powersoh/assets/97713997/194e5aef-b88a-4369-ad40-746e19145309" width="20%" height="20%">

<br>
<br>

* 데이터모양

<img src="https://github.com/mooncw/powersoh/assets/97713997/031a548e-25d1-44f2-b3d1-71b15358624e" width="40%" height="40%">

<br>
<br>

* 데이터 컬럼별 데이터 타입

<img src="https://github.com/mooncw/powersoh/assets/97713997/20a4fa5f-0db1-476e-a021-1444fe08fd54" width="20%" height="20%">

<br>
<br>

* 데이터 컬럼별 NaN 개수 확인

<img src="https://github.com/mooncw/powersoh/assets/97713997/511d63d2-58c4-4d78-be22-427b55295443" width="20%" height="20%">

<br>
<br>

**2)전력 데이터에 대한 도메인 지식 활용**
* Y결선임을 확인
<img src="https://github.com/mooncw/powersoh/assets/97713997/f3dad1bf-7be9-47d5-8792-3907ff4a69ff" width="80%" height="80%">

<br>
<br>

* 부하들의 주파수 확인
<img src="https://github.com/mooncw/powersoh/assets/97713997/686b51fe-8555-4969-b18e-64d635e196f4" width="40%" height="40%">

<br>
<br>

* 주파수가 0일 때 부하가 정지 상태이지만 역률이 경고로 뜸을 확인

<img src="https://github.com/mooncw/powersoh/assets/97713997/91d03b4e-68f4-40ab-8445-f85ab9ea0c32" width="60%" height="60%">

<br>
<br>

**3)NaN 케이스 확인**
* Case1 : '누적전력량', '유효전력평균', '무효전력평균', '주파수', '전류평균', '상전압평균', '선간전압평균', '온도'
* Case2 : 'R상유효전력', 'R상무효전력', 'R상전류', 'R상전압', 'R상선간전압', 'S상유효전력', 'S상무효전력', 'S상전류', 'S상전압', 'S상선간전압', 'T상유효전력', 'T상무효전력', 'T상전류',
 'T상전압', 'T상선간전압'
* Case3 : 'S상유효전력', 'S상무효전력', 'S상전류', 'S상전압', 'S상선간전압', 'T상유효전력', 'T상무효전력', 'T상전류', 'T상전압', 'T상선간전압'
* Case4 : 'T상유효전력', 'T상무효전력', 'T상전류', 'T상전압', 'T상선간전압'
* Case5 : ALL NaN

<br>

### 3. Feature Engineering
* 도메인 지식을 활용하여 결측치를 처리했습니다.
* 먼저 Case5에 해당하는 row들은 삭제하고 Case 별로 주파수, 온도는 바로 앞과 뒤의 평균값으로 처리하고 나머지는 다른 feature을 이용하여 구한 값으로 처리했습니다.
* 처리 후 결측치 확인

<img src="https://github.com/mooncw/powersoh/assets/97713997/5089ad43-9143-4a2f-8caa-a2055689712f" width="18%" height="18%">

<br>
<br>

* 도메인 지식을 활용하여 각 label마다 통합 or 선택하여 df를 만들었습니다.
* 역률

<img src="https://github.com/mooncw/powersoh/assets/97713997/e5abba9b-de08-4eba-bdf2-050b75717f79" width="38%" height="38%">

<br>
<br>

* 전류고조파

<img src="https://github.com/mooncw/powersoh/assets/97713997/a2d91447-48d3-4205-ae9a-4d39b2966cba" width="48%" height="48%">

<br>
<br>

* 전압고조파

<img src="https://github.com/mooncw/powersoh/assets/97713997/81c6cc89-12eb-47ce-9232-980799e9ca70" width="100%" height="100%">

<br>
<br>

### 4. Modeling

* train셋과 validation셋을 8:2로 나눈 후 standard scaler를 사용했습니다.
* 최빈값으로 베이스라인을 잡고 Random Search로 하이퍼 파라미터를 튜닝하여 RandomForest 모델을 학습시켰습니다.
  - 튜닝한 하이퍼 파라미터는 max_depth, n_estimators 입니다.
* 평가지표는 타켓 불균형이 있기에 각 타겟을 골고루 잘 예측했는지 확인하기 위해 macro_f1_score를 사용했습니다.
* 이 후, 지속적인 feature engineering을 하여 성능 개선을 했습니다.

<img src="https://github.com/mooncw/powersoh/assets/97713997/ba5c08e4-3589-4dad-8706-53811dc6809f" width="55%" height="55%">

<br>
<br>

## Label별 모델 결과 & 최종 macro_f1_score
### 1. 역률
 **베이스모델 - 최빈값**
</br>
![image](https://user-images.githubusercontent.com/97713997/195078577-26f399b5-f2d0-48b0-9ae2-b5a9582e6e91.png)
</br>
**macro_f1_score: 0.23**
</br>
</br>
</br>
 **랜덤포레스트**
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
![image](https://user-images.githubusercontent.com/97713997/195083359-a71ce0f9-a52e-4ec6-b442-57bcac8db5c1.png)
</br>
**macro_f1_score: 0.30**
</br>
</br>
</br>
 **랜덤포레스트**
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
![image](https://user-images.githubusercontent.com/97713997/195083927-d4a81f41-a448-4bfe-9862-cdc385d8c926.png)
</br>
**macro_f1_score: 0.19**
</br>
</br>
</br>
 **랜덤포레스트**
</br>
![image](https://user-images.githubusercontent.com/97713997/195084194-f5bc4a05-70a1-4b6f-a914-b5f9aca00cfd.png)
</br>
**macro_f1_score: 0.92**
</br>
</br>
</br>
### 4. 최종결과
![image](https://user-images.githubusercontent.com/97713997/195084384-c67e417f-0f03-4e92-ac95-bd82d14245ec.png)


## 만족스러웠던 부분
여러 시도의 feature engineering으로 성능 개선을 했습니다.

<br>

## 한계점 및 해결 방안
최종결과가 기대한 만큼 결과가 나오지 않았습니다.

더 좋은 feature 엔지니어링 방법이 있다면 결과가 더 좋아질 것 같습니다.

랜덤포레스트 분류보다 좋은 모델을 만들면 결과가 더 좋아질 것 같습니다.

label이 경고이지만 고장 상태인지 사용을 안하고 있는 상태인지 구분할 필요가 있어보입니다.
