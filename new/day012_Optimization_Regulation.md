# 최적화 관련 용어들

### Important Concepts in Optimization

* Generalization   
  Generalization Gap을 training 데이터에 대한 정확성과 test 데이터에 대한 정확성 사이의 차이를 말한다.  
  이 Gap이 작으면 Generalization이 좋다고 말한다.

* UnderFitting vs OverFitting   
  오버피팅은 학습데이터에 대해 잘 에측하지만 테스트 데이터에 대해서는 잘 에측하지 않는 것을 말한다.  
  언더피팅은 네트워크가 너무 간단하거나 학습이 부족한 것을 말한다.  
  
* Cross validation   

  학습 / 테스트 데이터를 나누는 기법 중 하나이다.  
  데이터를 일정한 간격으로 나누고 (k개의 fold) 그 중 하나를 validation data로 정한다.  
  validation data를 뺀 나머지 학습 데이터를 이용해서 학습하고 validation data를 이용해서 잘 학습되었는지 판단한다.  
  이 과정을 validation data을 돌아가면서 다른 fold로 지정하고 나머지로 학습하고 validation data로 정확성 판단을 반복한다.    
  
  Hyper Parameter를 찾는 경우에 쓰이고 있다. ※하이퍼 파라미터(= learning rate, network크기, loss function...)  
  Cross validation기법을 이용해서 적절한 하이퍼 파라미터를 찾은 후  
  전체 학습 데이터를 이용해서 학습을 시킨다.
  
* Bias and Variance
  * Bias
  정확한 값을 예측하지 못한다. (High Bias)
  * Variance
  유사한 데이터에 대해 예측값이 일정하지 못하다. (High Bias)
  
  서로간에 trade off가 적용될 수 있다.  
  cost를 minimize 한다는 것은  세가지 부분, bias, variance, noise로 나뉠 수 있다.
  
* bootstrapping  
  Bootstrapping is any test or metric that uses random sampling with replacement  
  전체데이터가 아니라 일부데이터를 이용하는 방법  
  그 중에서 ...  
  * Bagging (Bootstrapping aggregation)
    * 부트스트래핑으로 여러개의 모델을 학습시킨 후   
    여러 모델에서 나온 결과를 합계(voting or averaging)하는 방법
    
    * 앙상블(ensemble)이라고 불리고도 한다. 일반적으로 한가지 모델보다 좋은 결과과 나와서 캐글 등에서 많이 사용
  
  * Boosting
    * 일부의 데이터에서만 잘 동작하는 모델들(weak learner)을 만들고 그 모델들을 sequential하게 연결시키는 방법
    
---  

### Practical Gradient Descent Methods

* 그래디언트 3세지 분류
  * SGD : single data
  * Mini-batch : subset of data
  * Batch : whole data

* 배치사이즈는 많이 중요하다.
  * 큰 배치사이즈 -> sharp한 미니멈 위치에 도달한다
  * 작은 배치사이즈 -> flat한 미니멈 위치에 도달한다
  
  학습 function 상에서 flat한 미니멈 위치일 때,  
  테스트 function 과의 차이가 적을 것이므로  
  적은 배치사이즈를 사용하는 것이 더 좋다.  
  (반대로, sharp한 지점 주위에서는 데이터의 작은 차이에도 학습function과 테스트function의 차이가 클 수 있다)
  
  큰 배치사이즈의 활용은 논문참고?
  
* Gradient Descent Methods  
  
  * SGD
  
  * Momentum  
  이전 Gradient 방향을 기억해서 다음 그래디언트에 영향을 주는 방법.  
  Gradient 계산시 이전 Gradient에 하이퍼파라미터를 곱해준 값을 더해준다. (accumulation)  
  모멘텀은 최저점으로 빠르게 이동하지만, 부호가 바뀌어도 이전 방향으로 가기 때문에 최저점으로 수렴하지 못할 수 있다.
  
  * Nesterov accelerated gradient   
  Momentum 방법과 유사한데 gradient 대신 Lookahead gradient을 계산 후 모멘텀을 더해준다.    
  Lookahead gradient은 이동할 방향으로 미리 이동해서 gradient를 계산하는 방법이다.  
  
  * Adagrad
  네트워크의 각각의 파라미터에 대해 현재까지의 변화에 따라서 gradient의 양을 달리 계산한다.  
  많이 변한 파라미터들은 변화량을 줄이고, 조금 변한 파라미터들은 변화량을 늘리는 방법이다.
  
    학습을 오래하면 변화량이 줄어들어 문제가 된다.
  
  * Adadelta  
  Adagrad가 가진 단점을 해결한다.  
  윈도우 사이즈(일정 학습기간)만큼 동안만의 각 파라미터의 변화량을 본다.
  learning rate가 없다!
  
    Exponential Moving Average(EMA).

  * RMSprop  
    ?  
    
  * Adam  
  Adaptive Moment Estimation.    
  
  EMA of gradient squres를 사용해서 adaptive하게 learning rate를 바꾸는 것과  
  이전 gradient의 정보를 사용하는 momentum을  
  합쳐서 사용하는 방법  
  
--- 

### Regularization
학습을 방해하도록 규제를 둔다.      
규제의 이점은 학습데이터에서만 잘 작동하는 것이 아니라 테스트 데이터에서도 잘 작동되로록 만들기 위함이다.

* Ealry stopping   
  학습이 오래되면 학습데이터 오류는 줄어들지만 validation데이터 오류는 늘어난다.  
  이것을 방지하기 위해서 validation의 오류가 늘어나는 지점에서 멈춘다.
  
* Parameter Norm Penalty  ( = weight decay )  
  파라미터의 숫자가 너무 큰 값이 되지 않도록한다.  
  이것의 목적은 함수를 부드럽게 만드는 위함이다.
  
* Data Augmentation  
  주어진 데이터를 이용해서 새로운 데이터를 만든다.  
  예를들어 이미지의 경우 기존 데이터의 레이블을 바꾸지 않아도 될 정도로 변화를 줘서 데이터를 만든다.
  
* Noise Robustness  
데이터에 노이즈를 추가한다. 입력뿐아니라 weight에도 추가한다.  
경험적으로 성능이 증가한다고 한다.

* Label Smoothing
학습데이터를 서로 섞어준다. 데이터뿐만 아니라 label도 합치기도 한다
decision boundary를 부드럽게 만들어 주려는 방법이다.

  * Mix-up  
  예)개와 고양이를 합친 그림.
  
  * Cutout  
  예) 이미지의 일정영역을 빼버린다.  
  
  * CutMix   
  예) 이미지의 특정 영역을 잘라서 합친다.
  
* Dropout  
일부 뉴런의 weight를 0으로 설정한다. robust하도록 만드는 목적.

* Batch Normalization, BN
layer의 statistics를 정교화.  
예)파라미터들을 0 mean, unit variance하게 으로 만드는 작업    
일반적으로 layer가 많이 쌓이게 될때 batch normalization을 하면 성능이 올라간다. ( 경험적)  
  
  * variances  
  layer norm, instance norm, group norm
