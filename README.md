[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-24ddc0f5d75046c5622901739e7c5dd533143b0c8e959d652212380cedb1ea36.svg)](https://classroom.github.com/a/g6ZC_OOE)
# 업스테이지 서울 아파트 가격 예측 8조

## Team 8

| ![김소현](https://avatars.githubusercontent.com/u/156163982?v=4) | ![권혁찬](https://avatars.githubusercontent.com/u/156163982?v=4) | ![김태한](https://avatars.githubusercontent.com/u/156163982?v=4) | ![문정의](https://avatars.githubusercontent.com/u/156163982?v=4) | ![이현진](https://avatars.githubusercontent.com/u/156163982?v=4) |
| :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: | :--------------------------------------------------------------: |
|            [김소현](https://github.com/UpstageAILab)             |            [권혁찬](https://github.com/UpstageAILab)             |            [김태한](https://github.com/UpstageAILab)             |            [문정의](https://github.com/UpstageAILab)             |            [이현진](https://github.com/UpstageAILab)             |
|                            팀장, EDA, 피쳐 엔지니어링, 모델링                             |                            EDA, 피쳐 엔지니어링, 모델링                             |                            EDA, 피쳐 엔지니어링, 모델링                             |                            EDA, 피쳐 엔지니어링, 모델링                             |                            EDA, 피쳐 엔지니어링, 모델링                             |

## 1. Competiton Info

### Overview

- _Write competition information_
House Price Prediction 경진대회는 주어진 데이터를 활용하여 서울의 아파트 실거래가를 효과적으로 예측하는 모델을 개발하는 대회입니다. 

저희는 이러한 목적 하에서 다양한 부동산 관련 의사결정을 돕고자 하는 부동산 실거래가를 예측하는 모델을 개발하는 것입니다. 특히, 가장 중요한 서울시로 한정해서 서울시의 아파트 가격을 예측하려고 합니다.

### Timeline

- January 15, 2024 - Start Date
- January 25, 2024 - Final submission deadline


### Evaluation

- RMSE(Root Mean Squared Error)

## 2. Components

### Directory

이 부분은 수정 필요  
대회의 데이터 -> 베이스라인 코드 페이지 참조

|- Project_Folder  
| | code | model  
| | data | train.csv  
| | data | test.csv  

- _Insert your directory structure_

## 3. Data descrption

### Dataset overview

- 학습 데이터는 아래와 같이 1,118,822개이며, 예측해야 할 거래금액(target)을 포함한 52개의 아파트의 정보에 대한 변수와 거래시점에 대한 변수.
- 주요 데이터는 .csv 형태로 제공되며, 서울시 아파트의 각 시점에서의 거래금액(만원)을 예측이 목표
- 학습 데이터의 기간은 2007년 1월 1일부터 2023년 6월 30일
- 테스트 데이터의 기간은 2023년 7, 8, 9월. Puiblic과 Private은 각각 Random하게 5:5로 나뉜다.
- 변수 
- 연속형 변수:
  '전용면적', '계약년월', '계약일', '층', '건축년도', 'k-전체동수', 'k-전체세대수', 'k-연면적',
  'k-주거전용면적', 'k-관리비부과면적', 'k-전용면적별세대현황(60㎡이하)', 'k-전용면적별세대현황(60㎡~85㎡이하)',
  'k-85㎡~135㎡이하', '건축면적', '주차대수', '좌표X', '좌표Y', 'target'
- 범주형 변수:
  '시군구', '번지', '본번', '부번', '아파트명', '도로명', 'k-단지분류(아파트,주상복합등등)', 'k-전화번호',
  'k-팩스번호', 'k-세대타입(분양형태)', 'k-관리방식', 'k-복도유형', 'k-난방방식', 'k-건설사(시공사)', 'k-시행사',
  'k-사용검사일-사용승인일', 'k-수정일자', '고용보험관리번호', '경비비관리형태', '세대전기계약방법', '청소비관리형태',
  '기타/의무/임대/임의=1/2/3/4', '단지승인일', '사용허가여부', '관리비 업로드', '단지신청일'

### EDA

#### 김태한
- 구별 아파트 가격의 Boxplot
![gu_boxplot](./image/구별_가격_박스플랏.png)  
- 구별 연도에 따른 아파트 가격 추이 Plot
![gu_boxplot](./image/구별_연도별_아파트가격추이.png)  

### Feature engineering

#### 김태한
- 아파트 전용면적에 따른 클래스 생성 후 추가 
```
def class_area(data):
    if data < 40: #16평 이하
        result = 'small'
    elif data >= 40 and data < 61: #16~24평
        result = 'mid-small'
    elif data >= 61 and data < 86: #24~33평
        result = 'middle'
    elif data >= 86 and data < 132: #34~40평
        result = 'mid-large'
    elif data >= 132 and data < 166: #41~50평
        result = 'large'   
    else: # 51평 이상
        result = 'huge'
    return result
```
- 엄효범님 코드에서 아이디어를 얻어, 추론할 y값을 아파트 전용면적 클래스 별 평균값으로 대체 
```
train_x['price'] = train_x.groupby(['area_class'])['target'].transform('mean')
```

- _Describe feature engineering process_

## 4. Modeling

### Model descrition

- XGBoost
  : 기존 Gradient Boosting Tree 모델에서 HW적인 최적화를 수행한 모델.
    Depth-wise(Level-wise) Growth 모델이다.

- LGBM (Light GBM)
  : SW적인 알고리즘적 최적화가 잘 된 모델.
    LGBM은 Leaf-wise Growth 모델이다.

### Modeling Process

- _Write model train and test process with capture_

## 5. Result

### Leader Board

- _Insert Leader Board Capture_
- _Write rank and score_

### Presentation

- _Insert your presentaion file(pdf) link_

## etc

### Meeting Log

- [1주차](https://www.notion.so/1-1-7-1-12-056b6469b2d944599317d98271a6e433)
- [2주차](https://www.notion.so/2-1-15-1-19-de33ab19686b4e8ba5b95c393801f416)
- [3주차](https://www.notion.so/3-1-22-1-25-5e69f667552940728f8f73281a5dd93e)

### Reference

- [2023년 4분기 주택 매매 가격 추세 기사](https://www.m-i.kr/news/articleView.html?idxno=1084731)
- [2023년 전국 주택 매매 가격 추세 기사](https://www.segye.com/newsView/20240115513617?OutUrl=naver)
