# 강의 1 - Generative Models 1  

* 전체적으로 무슨 말인지 이해가 안된다.  
**추가학습**  필요

* Generative model 이란?  
  일반적으로 이미지나 문장 등 데이터를 생성해 내는 모델을 말한다.   
  하지만 그런 생성의 의미만 있는 것이 아니다. 구체적으로,
  
  1. Generation : p(x)를 따르는 새로운 x 생성 = 강아지 이미지 생성  (implicit 모델)  
  
  2. Density estimation :  받은 이미지가 강아지인지 아닌지 구별,  
    p(x)가 높으면 강아지처럼 생겼을 듯하다. -> 이상감지모델(anomaly detection)에서 사용...    
  
    - 마치 Discriminator 같다... 즉, 엄밀히 말하면 Generative 모델은 Discriminator 모델을 포함하고 있다.
  
    - (explicit 모델) -> 이 뜻은 어떤 값이 들어오면 확률값을 얻어 낼 수 있는 모델을 말한다
    - (implicit 모델) -> 반대로 단순히 generation 만 할 수 있는 모델을 말한다. (VI, GAN)  
    - 그렇다는 것은 generative 모델은 explicit , implicit 둘 중 하나로 나뉠 수 있다는 걸 말한다.
  
  3. Unsupervised representation learning : 이미지의 특징을 학습한다. feature learning   
  
  
 ### p(x)를 어떻게 찾아내는가?
 
* 일단 확률 기본 지식
* discrete 분포  
  - 베르누이 : 2개,   
  확률분포를 표현하려면 숫자 1개 필요  
  - 카테고리컬 : 여러개,   
  확률분포를 표현하려면 숫자 n-1 개 필요  
  예) 주사위
  1일 확률, 2일 확률, ..., 5일확률만 알면  
  6일 확률은 전체에서 나머지를 빼면 알아서 구해진다  
  
  - RGB 이미지 픽셀은?  
0 ~ 255 사이 숫자가 각각 나올 확률이 필요하고, 각 R,G,B는 독립적으로 3개 필요하고,   
위의 주사위처럼 하나당 n-1 개의 파라미터가 필요하므로 픽셀하나에 255 x 255 x 255 = 16,581,375 개가 필요하다.  

  - 바이너리 이미지?  
상태(state), 표현할 수 있는 이미지는 2^n개이다. (n = 픽셀수)
파라미터는 2^n - 1개 가 필요하다  

* 위를 살펴보면 알 수 있듯이 필요한 파라미터 수가 너무 크다.  
  그리고 기계학습에서 파라미터가 늘어나면 학습이 어렵다
  
* 다시 바이너리 이미지로 돌아와서      
이미지 안의 모든 픽셀들이 서로 independent 하다고 가정하면 파라미터의 수를 N개로 줄일 수 있다...
픽셀들이 서로 독립적이라고 가정했기 때문에 픽셀 하나씩의 관점(?)으로 보게 되고, 픽셀 하나를 구하는데는 파라미터개 1개 밖게 들지 않으므로 전체 파라미터는 N개만 필요하다.   

  p(x1,x2,...,xn) = p(x1)p(x2)...p(xn) 과 같은 수식이 가능해진 것과 같다. (독립분포의 결합분포 ??? 수식과 용어는 잘 모르겠다)   

* 그런데 위와 같이 가정하면 파라미터는 줄어드나 표현할 수 있는 것이 없어지므로(실제로는 이미지안의 픽셀들은 서로 dependent하다) 중간점을 찾기로 한다.  
그 중간점을 찾기 위한 방법으로 **Conditional Independence** 라는 것을 사용하게 된다.

#### Conditional Independence (조건부 독립)  
http://norman3.github.io/prml/docs/chapter08/2.html

<img src="https://github.com/bitwarrior1/bcaitech/blob/main/new/img/conditional_independence.png" width="700" height="300">



* 중요 3가지 법칙
1. chain rule (exact)   
joint rule 을 chain rule로 표현하는데 추가 가정은 들어가지 않았다.
달라진게 없으므로 파라미터는 동일.  
이미지의 확률분포가 픽셀들의 dependent한 chain rule로 표현 가능한걸 알 수 있다.(**맞나??**)

2. Bayes' Rule (exact)  

3. Conditional Indenpendence (가정)   
dependent 삭제

* 1과 3일 합친 개념인 Markov assumption을 사용한다.  
예를들어 픽셀 는 i-1픽셀에만 dependent하고 1~i-2까지의 픽셀들에는 independent하다. 라고 보는 것   
이 경우에 파라미터는 2N - 1 개가 필요하다.  
(depedent한 변수의 수는 1개로 고정된 것이 아니라 변경가능)

#### 위에서 살펴본 방법(= conditional independence)을 Autoregressive model(=AR model)라고 한다.


### Auto-regressive Model  
join distribution 을 chain rule을 가지고 conditional distribution으로 쪼개는 방식을 활용  

네트워크상에서 p(x)를 어떻게 만들고 쓰것인가? 

1. autoregressive model 사용한다       
  하나의 정보가 이전 정보에 dependent하다. (모두에 dependent하든 하나에만 dependent하든)
  
2. ordering이 필요하다

### 모델들

## NADE  
* 이전 모든 픽셀들에 dependent 하다
* 이전 픽셀들의 입력값으로 받고 
ouput을 scalar 값으로 주는
그리고 거기에 시그모이드가 합쳐진 NN을 구성.

* 이전 픽셀들의 수가 늘어나므로 매번 입력 차원이 달라지므로 weight도 점점 증가하는 특징.  

* 입력에 대한 확률값 계산도 가능한 모델이다 (=Density Estimator, 의미는 explicit 모델을 뜻하는 경우가 많다)  

* In case of modeling Continuous random variables,  
a mixture of Gaussan can be used. (뭔뜻??, Continuous random varible 예시는 뭐가 있을까??)

### Pixel RNN  

* 위의 모델은 fully conntected, 이건 RNN 형태이다  

* ordering을 어떻게 처리하냐에 따라서 두가지 알고리즘이 나온다  
  1. Row LSTM 
  2. Diagonal BiLSTM  
  
#### 지금까지 알아본것, (테스트)
1. Generator Model이 어떤것인가?
2. Generator Model이 단순히 무언가를 생성하는 implicit 모델만 있는 것이 아니라 explicit모델도 있다  
3. 파미터 개수와 관련해서...   
fully dependent 모델,  fully independent 모델의 중간점인 **conditional independence**와  
**chain rule**를 같이 사용하는 Auto-regressive 모델  

# 강의 2 - Generative models 2  

* Variational Inference (VI)  
  Posterior distribution을 구할 수 없으니 그것의 근사인 Variational Distribution을 구하는 목적으로 사용한다.  
  Posterior distribution란? : observation이 주어졌을 때 관심있어하는 random variable의 확률분포  

* GAN  
  generator와 discriminator간의 경쟁을 통해서 generator의 성능을 향상시키는 모델    
    * generator  
    이미지를 생성해서 discriminator을 속인다.  
    training 데이터의 분포를 학습한다.(=실제 이미지들의 확률분포에 근접한다.)  
    
    * discriminator  
    진짜 이미지와 비교하여 만들어진 이미지를 구분한다. (=분류 문제)   

## 추가  
https://www.youtube.com/watch?v=o_peo6U7IRM&ab_channel=naverd2

* Autoencoder  

입력과 출력이 동일한 값을 가지도록 만드는 네트워크 구조를 가진다.  

[학습할 때]  
학습방법이 unsupervised 하며,  
loss는 negrative ml(maximum Likelihood)로 해석된다.  

[학습된 오토인코더]  
인코더는 차원축소 역할 -> Manifold learning  
디코더는 생성모델의 역할 -> Generative model learning  

