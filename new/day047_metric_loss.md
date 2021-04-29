## 컴페티션
* DataLoader의 num_workers가 1이상이면 멈춰서 0으로 설정했는데 매우 느려졌다...
* 피어세션에서 추천받은 방법들을 적용해보고 있다.
* optimizer나 criterion, 등등 모듈마다 사용하는 parameter가 달라서 argparser로 컨트롤하려면 추가 코드를 작성해야한다.
* kfold를 적용했더니 DataLoader나 쓰다 남은 변수들(아마 gpu에 할당된?)이 남아있는건지 memory 오류가 난다.

## 피어세션
* 많은 정보를 공유해주셨다.
* 매일 주는것보다 얻어가는 것이 큰것 같다...

## 학습한 몇가지 개념 - 틀릴수있다.
#### 1. Entropy  
* 정보를 표현하기 위한 최소한의 자원  
  표현하는 자원은 bit로 구성할 수 있다.  
  자주 등장하는 정보는 작은 자원으로 표현한다.  
  드물게 등장하는 정보는 큰 자원으로 표현한다.  
  이것을 나타내는 식으로 -log 함수를 생각할 수 있다.  
  정보의 등장은 확률P(x)로 생각할 수 있고 그렇다면 정보(의 표현을 위한 자원)량은    
  <a href="https://www.codecogs.com/eqnedit.php?latex=-\log_{2}P(x)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-\log_{2}P(x)" title="-\log_{2}P(x)" /></a>  
  로 나타낼수 있다.

* 전체 확률분포에 대해서는  
  <a href="https://www.codecogs.com/eqnedit.php?latex=-\sum_x&space;P(x)\log_{2}P(x)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-\sum_x&space;P(x)\log_{2}P(x)" title="-\sum_x P(x)\log_{2}P(x)" /></a>  
  로 나타낼수 있다.  
  이 값은 확률분포가 uniform 일때 가장크다.   
  이유는 생각해보면 어느 하나가 다른 것들보다 더 잦게 등장한다면 그 만큼 작은 자원으로 표현할수 있는데 Uniform하다면 그럴 수 없다.
  특정 정보를 적은 자원으로 표현해도 균등하므로 그 수가 다른 것들보다 많지 않기 때문이다.
  

* Cross Entropy  
내가 설정한 q에 대한 Entropy (최소량 or 이상적이지 않음)  
<a href="https://www.codecogs.com/eqnedit.php?latex=-\sum_x&space;P(x)\log&space;Q(x)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-\sum_x&space;P(x)\log&space;Q(x)" title="-\sum_x P(x)\log Q(x)" /></a>  

* KL-divergence  
이상적인(최소자원의) entropy 와 Cross Entropy의 차이  
**그래서 KL-divergence를 최소화하는 것은 Cross Entropy를 최소화하는 것과 같다**   
loss로 cross entropy를 사용하는것이 이해가 됬다.


#### 2. Label Smoothing
* target의 label 값이 [0,0,1] 같은 ont-hot이었다면   
  Label Smoothing을 적용해서 [0.2, 0.2, 0.8] 같은 형태로 바꾸는 것이다.  
 
* Regularization의 한가지 방법이다. 오버피팅을 방지한다.

* 레이블에 노이즈를 적용하는 개념으로 생각할수 있다.  
* 데이터는 오류(?)를 가지고 있으므로 데이터에 맞게 확률(likelihood ?)을 최대화하는 것은 위험하다고 보는 가정이 들어있다.
  
  <a href="https://www.codecogs.com/eqnedit.php?latex=y^{LS}&space;=&space;y(1-\alpha&space;)&space;&plus;&space;\alpha&space;/&space;(K-1)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?y^{LS}&space;=&space;y(1-\alpha&space;)&space;&plus;&space;\alpha&space;/&space;(K-1)" title="y^{LS} = y(1-\alpha ) + \alpha / (K-1)" /></a> 
     
     K는 클래스 수
  
#### 3. F1 - score
* Metric의 하나이다.
* imbalanced 가 있을때 사용하기 좋다. (micro)
* recall을 반영한다. "1"이 반영 정도를 나타내는 듯 하다.
* micro, macro 방법등이 있다.
* 2 x (precision * recall) / (precison + recall)

1. macro
* 현재 컴페티션에서 사용중.
* 각 클래스관점에서 F1 score를 구하고 평균낸다.(18클래스면 f1 score 18개를 구한다)
* 평균낼때 weight를 적용하기도 한다.
* imbalanced가 결과에 반영되지 않으므로 추가로 표준편차를 사용하기도한다.

2. micro
* imbalanced일때 좋다
* **문제는 multi classification일때 사용할 수 없다**   
  precision, recall구할때 tp,fp,fn을 합계해서 계산한다.  
  하나의 클래스에서의 fp는 예측값에 해당하는 다른클래스의 fn이 된다. (fp = fn)  
  precision=recall=f1=accuracy   

#### 4. Focal Loss
* cross entropy변형  
  
<a href="https://www.codecogs.com/eqnedit.php?latex={\text{FL}(p_{t})&space;=&space;-&space;(1&space;-&space;p_{t})^\gamma&space;\log\left(p_{t}\right)}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?{\text{FL}(p_{t})&space;=&space;-&space;(1&space;-&space;p_{t})^\gamma&space;\log\left(p_{t}\right)}" title="{\text{FL}(p_{t}) = - (1 - p_{t})^\gamma \log\left(p_{t}\right)}" /></a>  

 * 맞춘 확률이 높은 Class는 낮은 loss를,  맞춘 확률이 낮은 Class는 높은 Loss를 
