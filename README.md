# 2022-2 소프트웨어융합캡스톤디자인

## 과제명 : Forecasting 정확도 개선 및 계산량 감소를 위한 ML 전이학습 기법 연구:M5 Competition에 적용

### 과제 개요 
#### 과제 설계 배경 및 필요성 
- 대부분의 많은 카테고리의 산업에서 어떠한 사업을 기획할 때 항상 시작은 demand forecasting으로 시작합니다. 예를 들어 금융 분야에서는 과거 고객 재무 정보를 토대로 심사를 고도화하고 리스크를 관리하는 데에 쓰일 수 있고, 마케팅과 세일즈 업무에서는 이벤트의 성공률이나 제품의 구매율을 예측하여 타겟 고객을 엄밀히 세그먼팅할 수 있으며, 제조 분야에서는 불량품을 자동으로 검출하고 공정 프로세스를 최적화하는 데에 쓰일 수 있습니다. 
  이러한 demand forecasting 관련 competition으로 약 40년 전 M1이 처음 열렸고 그 당시에는전통 통계 기반인 ARIMA관련 모델이 성능이 가장 정확하게 나왔습니다. 이런 전통 통계 기반 모델들이 2000년에 열린 M3 대회까지 가장 좋은 성능을 내었습니다. 하지만 M4에서는 전통 통계 그리고 ML 기법을 통합한 모델이 top-score를 기록하였고 가장 최근 대회인 M5의 수상팀들은 과거와 다르게 ML/DL 모델들이 top-30 안에 들어간 비율이 높아졌습니다. 이렇듯 점점 demand forecasting 분야에서 ML/DL이 차지하는 비중이 높아짐에 따라서 해당 주제에 대한 ML/DL 기술 접목에 대한 연구가 중요하다고 생각합니다.

#### 과제 주요내용
m5는 forecasting 관련 대회이며 약 5년의 Walmart retail data를 활용하여 추후 28일의 retail demand를 정확하게 예측하는 것이 과제였습니다. 

 해당 대회의 결과를 간단하게 요약하자면 이전 forecasting 대회보다 대다수의 우승팀들의 방식이 
ml로 전환되는 양상이 나왔습니다. (top50 중 45팀이 LGBM 사용) 또한 많은 팀들이 데이터 구조의 
특성상 각 계층마다 다른 model을 생성하였습니다. 이러한 방식은 학습시키는 모델 수의 증가를 불
러왔습니다. winning method 또한 220개의 model을 생성 후 각각 처음부터 새로 학습을 시켜서 예
측을 수행을 하였습니다. 대다수의 winning method에서 소개한 다중 모델 생성 방식으로 인한 문제점은 아래와 같습니다. 이는 m5 competition 관련 여러 논문에서 발표한 문제점들을 정리한 것입니다.
 
- 1. 단일 모델이 아닌 데이터의 계층 단위로 모델을 생성한 경우에 각각 모델에 대한 최적화를 진행하는데 무리가 있음
- 2. 계절성 또는 판촉 효과와 같은 명백한 유사점이 시계열의 계층 구조 전체에 존재함에도 불구하고 이러한 방법은 각 상점, 카테고리 또는 부서에 대해 여러 ML 모델을 처음부터 교육해야 한다는 사실에서 계산 부담이 크게 발생
- 3. 추후 실제 산업에 적용 시 계산량에 대한 부담 때문에 잘 진행이 안 될 수 있음
- • 데이터가 더 많은 case에 적용하려면 많은 computing resource 가 필요
- 4. 상대적으로 데이터의 크기가 작은 최하위 계층에 대해서는 예측 정확도의 성능이 저조

#### 최종결과물의 목표
모든 계층별로 (store:10개) 전부 하이퍼 파라미터 튜닝 그리고 fully training을 시키는 것이 아닌 Base 모델을 한 개 설정 후 해당 모델만 하이퍼 파라미터 튜닝을 해서 모델 구조 전이를 통한 다른 store 혹은 state에 대해서 예측을 수행했을 때 computational cost는 감소되고 성능은 fully train 시키는 경우의 값과 비슷하거나 우수하게 나오는 것이 목표입니다.

- 1. 정량적 목표: Transfer-ML의 성능을 WRMSSE, RMSE, Time Cost 측면으로 기존의 방법론들과 비교 분석
- 2. 정성적 목표: Transfer-ML의 시도를 통한 실험 과정, 시행착오 및 추후 연구 개선점 정리 

### 과제 수행방법
1. 전이학습은 수평구조로 진행
Store: [CA1~4, TX1~3, WI1~3]

2. 데이터 사용
5Y 모두 사용시 한번 실험을 전개하는데 1일 이상이 소요(실험 진행이 단기간 안에 현실적으로 불가)
2_1. 베이스 모델: 3Y(최근)
2_2. 전이 받는 모델: 1Y(최근) 사용 
2_3. Test: m5 competition test set

3. 데이터 전처리 
M5 winning method의 feature를 사용하였습니다. 

4. 베이스 모델 학습 및 하이퍼 파라미터 튜닝 
ML 모델은 winning method 중 가장 큰 비중을 차지한 LGBM 고정
4_1. 베이스 모델로 선정한 Store의 3Y 데이터를 활용해서 하이퍼 파라미터 튜닝 실시
이전 winning methods들의 하이퍼 파라미터 참조하여 여러 가지 경우 조정하면서 실시
4_2. 여기서 베이지안 하이퍼 파라미터 튜닝 실시(시간 절약 측면)

5. 베이스 모델을 각 Store에 전이
5_1. 베이스 모델을 학습한 Store를 제외한 다른 Store를 예측하는 모델을 만들기 위해 베이스 모델 불러옴 
5_2. 전이시키는 Store에 대해서 최근 1Y 데이터를 활용하여 refit을 이용해 학습 
- 베이스 모델 기준 예측 결과와 새로 refit한 예측 결과의 가중치 비율을 조정하는 decay_rate를 (0.9~0.0) 조정하면서 실험 
5_3. 2의 결과 모델을 사용해서 validation set 평가
    - RMSE로 평가 
5_4. 학습된 모델 (베이스 모델: 1개, 전이 받은 모델:9개) 저장 

6. 4~5 단계를 Store 개수만큼 10개만큼 반복

7. 비교군 생성
1_1. 각 Store별 1Y 데이터 활용해서 fully train 실시 모델 생성 
1_2. 각 Store별 3Y 데이터 활용해서 fully train 실시 모델 생성 
 
8. Test set에 대한 결과 도출 
1. m5 공식 구글 드라이브에 나와있는 산출 R 코드 수정 후 사용
2. competition test set을 활용해서 각 모델에 대한 결과 도출 후 R 코드에 입력 후 결과 도출 

9. 결과 정리 
9_1. 각 Case 별로 나온 결과를 WRMSSE, RMSE 그리고 training time cost 기준으로 평가 및 정리

### 수행결과
- 본 프로젝트는 “Yang S., Lin C., Liao K., et al.: Progressively complementary network for fisheye image rectification using appearance flow. In: IEEE/CVF conference on computer vision and pattern recognition (2021)” 논문에서 소개된 딥러닝 모델(PCN)과 데이터셋(Places2, 256 * 256 image)을 이용해 진행했다.
- Ground Truth 와 fisheye image 를 training set 각각 40000 장씩, testset 은 각각 10000 장씩 생성해 총 30 epoch 을 학습하는 걸 기준으로 실험들을 진행했다.

#### 과제수행 결과


#### 최종결과물 주요특징 및 설명

1. Base 모델을 어떤 store기준으로 잡느냐에 따라서 전이 학습에 대한 성능이 크게 달라집니다. 
- 이는 store별로 가지고 있는 데이터의 특성 차이에 따른 결과로 보여집니다.
- 이 부분에 대해서 데이터 분포와 결과를 더 연구해야 추후 연구 결과가 더 나아질 것이라 생각합니다.

2. 대부분의 store에 대해서 fully train을 수행한 모델이 성능이 제일 우수하게 나옵니다.
- validation의 rmse에서도 그렇고 wrmsse에서도 fully trained 된 모델이 성능이 매우 높게 나옵니다.
- rmse에서는 몇 개의 CA3의 경우에서 우수하게 나오는 경우도 있지만 WRMSSE 값의 차이가 크게 나는 것을 보아 RMSE과 차이가 크다고 생각합니다. 
- 1의 결과와 같이 놓고 생각해보면 이는 전이하는 방식에 있어서 단순 수평 전이로는 원하는 결과가 도출되지 않는 것을 알 수 있습니다.

3. 학습 시간 측면에 있어서는 전이 학습 방식이 약 3.5배 정도 빠른 것으로 나타납니다. 
- 이는 전이 받는 모델은 모든 데이터를 다 사용하는 것이 아닌 최근 1Y 데이터를 활용해서 재학습을 진행하였기 때문에 나온 결과이며 해당 연구의 1차 목표이기도 합니다. 

4. Store 별로 크게 달라지는 것을 보완하는 방법으로 전이 학습을 실행해야 성능이 개선될 것으로 예상 되어집니다. 

5. Store가 아닌 State level에서 즉 좀 더 다양한 데이터에 대해 학습을 한 단위에서 수평 전이를 실시해봐야 합니다.
- store 단위에서도 학습, 예측, 평가 소요시간이 길어서 학기 중에 한계가 있었습니다. state level에서의 실험을 방학 중에는 수행이 가능할 것으로 생각합니다. 

6. refit의 decay_rate는 0.9~0.0까지 설정하면서 실험을 진행한 결과 0.0일 때 가장 성능이 좋았습니다. 
- 이는 베이스 모델을 전이 받은 모델은 refit을 통해 목표 Store인 최근 1Y 데이터를 활용해 store의 특성을 학습하며 기존 모델의 최종 예측값을 사용하지 않는 것이 더 좋은 예측 정확도를 보이는 것이라고 할 수 있습니다. 

최종적으로 제가 실험 이전에 새웠던 결과 예측이 시간 절약 측면에서만 이득이 존재하고 성능이 뒷받침 되지 않는 것으로 보입니다. 심지어 rmse가 basic보다는 전이학습이 우수하게 나왔는데 test set의 wrmsse로 보았을 때는 성능이 현저히 떨어지는 것을 볼 수 있었습니다. 이에 대한 원인을 위에서 정리한 것으로 추렸고 추후 추가적인 연구 및 수정이 필요할 것 같습니다. 

### 결론 및 제언
- 실험의 결과가 사전에 정해둔 목표와 동일하게 나오지는 않았습니다. time cost면에서는 감소가 있었지만 성능 측면에서 비슷하거나 우위를 가져가지 못하였습니다. 하지만 실험의 결과를 미루어보아 전이를 하는 방식에 있어서 해당 실험처럼 무작정으로 하나의 store모델을 학습하고 이외의 모델에 전이를 하는 것이 아닌 데이터의 유사성에 대해서 사전에 검토 후 유사성이 높은 것에 대해서 전이 학습을 진행을 한다면 실험의 결과가 더 좋아질 것으로 생각이 되어집니다. 또한 베이스 모델을 튜닝하는 방법에서도 해당 store에만 overfitting 되지 않도록 robust하게 학습하는 것을 시도해 본다면 충분히 결과가 나아질 것으로 기대합니다. 위에서 언급한 개선 사항에 대해서는 추후 방학 기간에 지금까지 나온 결과를 토대로 연구방식에 대해 발전시켜 향상된 결과를 도출하도록 하겠습니다.

### 참고자료
#### 논문
- Arnoud P. Wellens, Maxi Udenio, Robert N Boute. “Transfer learning for hierarchical forecasting: Reducing omputational efforts of M5 winning methods“ International Journal of Forecasting.(2022)https://doi.org/10.1016/j.ijforecast.2021.09.011
- S. Makridakis, E. Spiliotis and V. Assimakopoulos, M5 accuracy competition: Results, findings, and conclusions. International Journal of Forecasting (2022), https://doi.org/10.1016/j.ijforecast.2021.11.013.

#### dataset
- https://drive.google.com/drive/folders/1D6EWdVSaOtrP1LEFh1REjI3vej6iUS_4?usp=sharing
#### 그 외 참고 홈페이지
- https://github.com/Mcompetitions/M5-methods (m5 winning methods code)

