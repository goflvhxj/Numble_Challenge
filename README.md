# NUMBLE Challenge
진행 시기 : 2022.08 ~ 2022.11
<br/> 
<br/> 


## Mission : 비재무 데이터를 활용한 중소기업 휴.폐업 예측 모형 구축

**세부 미션**

****1. 데이터 가공 및 분석****

- 외부에 공개되어 있는 데이터를 직접 수집하고 결합해 새로운 변수를 생성
    - 예시 : 공공데이터 포털, 서울 열린 데이터 광장 등 업로드 된 공공 데이터 및 뉴스기사, 댓글 등 비정형 데이터
    - 데이터를 가공해 신규 파생변수를 생성하고 기술통계 분석 및 시각화 등 탐색적 분석을 통한 인사이트 제안  
    <br/> 
****2. 중소기업 휴.폐업 예측 모형 구축****
- 나이스 제공 데이터에 외부 데이터를 결합해 설명변수로 활용하고 부도 여부를 타겟 변수로 활용
    - Logistic Regression, Random Forest 등 분류 모델링을 통해 기업 부도를 예측
    - 평가 지표 : Accuracy, AUC 등
<br/>  
  
## 진행 과정  

### **1. EDA**
- 기본 제공 데이터 칼럼 용어 정리 및 분포 확인
- 27개의 파생변수 생성
- 수치형, 범주형 변수 확인 및 잘못된 타입 변경
<br/> 

### **2. 데이터 전처리**
- 사업자번호를 pk로 설정하여 기업명, 종업원수, 설립일자, 상장일자를 재무정보와 join
- 설립일자, 상장일자 업력 계산 (월 단위)
- 휴업 = 0, 가동 = 1로 label 생성
- 비율로 나타내는 모든 변수를 % 단위로 통일 <br/> 
- 결측치 처리
- 수치형 변수 범주형으로 변환 (binning 기법으로 gcut 사용)
    - 구간마다 데이터 개수가 동일하도록 7개의 구간으로 분류
    - 성장성, 수익성, 안정성과 같은 재무 지표에 따라 피처 분류 (지표파악 불가)
    - 구간별 부도율 계산하여 계속 증가하거나 감소하는 추세를 보이는 피처만  지표 별로 몇개씩 선정 (지표파악이 안되서 못함)
    - 모든 데이터가 범주형 변수로 변환된 상태로 최종 데이터 준비 완료
    
<br/> 

### **3. 변수 선정**
**방법 1 : binning + 단변량 분석** <br/> 
- binning 후 구간별 부도율 추세로 1차 필터링
- 1차 필터링한 피처들을 R에서 로지스틱 회귀로 단변량 분석 실시하여 p-value < 0.05인 피처만 2차 필터링
<br/> 

**방법 2 : 랜덤 포레스트로 변수 선정** <br/> 
- 랜덤포레스트로 산출된 변수 중요도를 기준으로 상위 20개의 변수 선택
<br/> 

### **4. 외부 데이터 선정**
- 서울경제와 중앙일보의 기사 등을 통해 종업원 수가 기업 부도율 간의 상관관계가 존재한다는 Insight를 도출 <br/>
- 수익성(영업이익, 당기순이익)과 안정성(자본금, 판매비와 관리비)의 주요지표와 종업원 수의 비율을 데이터로 활용

<br/> 

### **5. 모델구축**
- 모델 : AutoML인 Pycaret 라이브러리 이용하여 AUC 기준으로 top5 모델 앙상블
<br/> 

**기존 변수들만 모델링** <br/>

- binning + 단변량 분석으로 선정한 변수들로 모델링 시 AUC = 0.9366
- 랜덤 포레스트로 선정한 변수들로 모델링 시 AUC = 0.823
<br/> 

**binning + 단변량 분석 변수에 외부 데이터 추가하여 모델링**<br/> 

- 비재무 데이터를 결합하여 AutoML을 실행하였을 때 AUC가 0.9366에서 0.9527로 향상됨

