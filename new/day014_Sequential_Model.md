## 강의

* sequential 형 데이터의 문제  
입력값의 크기가 정해지지 않았다.

* Naive sequence model  
  다음 값을 예측하는 모델.
  고려해야 할 이전 값들의 수가 점점 늘어난다.

* Autoregressive model  
  고려해야 할 이전 값들의 수를 고정시킨다.  
  AR-1 : 고려해야 할 이전값이 1개  
  AR-2 : 고려해야 할 이전값이 2개  

* Markov model (first-order autoregressive model)  
  과거 데이터 중에서 몇개만 볼 것인지를 정한다.
  Easy to express the joint distribution...

  단점 : 과거의 많은 데이터를 고려할 수가 없다
  
* Latent autoregressive model
  중간에 hidden state를 추가한다. 이 hidden status에 과거의 데이터가 합쳐져(요약) 있다고 볼수 있다.  
  그리고 다음 time state은 이 하나의 hidden state에만 디펜던트하다.  

  (latent state = hidden state)
  
* 위의 컨셉들은 구현한 것이 RNN  

#### RNN

* 구조
  시간대별로 unroll 하면 입력과 출력이 매우 많은 구조임을 알 수 있다.   
  파라미터를 share하는 mlp와 같은 구조라고 볼 수 있다.

* Short-term dependencies
  먼 과거의 데이터가 학습에 영향을 미치지 못해서 비교적 현재에 가까운 과거데이터에만 dependent하다.  
  이 문제를 해결하기 위해 LSTM이 나오게 되었다.
   
* 왜 학습하기 어려운가?
  반복되는 구조로 되어 있는데,  
  activation function에 의해서 vanishing 또는 exploding 이 일어난다

* LSTM - Long Short-Term Memory

인풋:  
1. 현재시점의 입력. ex) 단어  
2. Previous cell state : 이전 까지의 정보를 취합해 summarize한 정보, 내부에서만 흐르는 정보
3. Previous hidden state : 이전 단계에서의 아웃풋과 동일한 값

아웃풋:  
1. Output (hidden state). 실제 출력값. ex) 다음 단어 예측
2. next cell state
3. next hidden state

게이트 단위로 알면 좋다. 4개의 게이트가 있다.
1. Forget gate : 어떤 정보를 버릴지 결정
2. Input gate : 어떤 정보를 저장할지 결정
1과2를 cell state에 update한다 (Update cell)
3. Output gate : output 계산시 cellstate도 사용한다.

* GRU _ Gated Recurrent Unit  
게이트가 2개뿐이다.  
No cell state, just hidden state.

__추세는 transformer  
sequencial 데이터는 전처리가 많이 필요하다.  
RNN 파라미터가 제법 많이 든다.__


## 강의 2 - Transformer

* Sequential Data를 다루는 하나의 방법론이다.  
* 순서가 바뀌거나 일부가 빠지는 경우의 문제를 해결하기 위한 방법으로 Transformer 가 등장한 것이다.  
* 트랜스포머는 self attention이라는 구조를 사용한다. RNN과 같은 재귀하는 구조가 없고 attention이라는 구조를 사용했다.  
* sequencial 한 데이터를 인코딩하는 기법이기 때문에 NLP문제에서만 국한되는 것은 아니다. 이미지분류, 디텍션등에서도 사용한다.  
* 대략적으로 프로세스를 보자.  
문장(단어 N)개가 들어오고 벡터형식의 데이터로 embedding 한다.  
Encoders (stacked)를 거쳐서 Decoders (stacked)로 나온다.  
그 후 Decoder를 generate 시켜서 결과값(문장)이 나온다. 출력 단어의 수는 달라졌을 수 있다.  

* 구체적으로 3가지 질문을 생각해보자.  
1) 어떻게 N개의 단어가 한번에 Encode 될 수 있는가?  
2) Encoder와 Decoder 사이의 어떤 정보를 주고 받는가?  
3) Decoder가 어떻게 generation 할 수 있는가?  
여기서는 1, 2번에 대해 다룰 것이다.

#### 1\) 어떻게 N개의 단어가 한번에 Encode 될 수 있는가? 

* Encoder 하나에 N개의 단어가 들어간다.  
  Encoder 내부를 보면 self-attention과 feed forward Neural Network로 나누어졌음을 알 수 있다.
  이 Encoder를 반복해서 거친다.

* feed forward Neural Network은 이전까지 배운 nn 구조이다.
* self-attention을 보면 데이터 3개 (예를들어 단어3개) 가 들어오면  
  동일한 개수의 출력값을 내놓는데, 이때 출력값 1개를 구할때 모든 데이터를(모든 단어,3개) 고려해서 구하게 된다.  
  각 단어가 다른 단어에 dependency 하는 것 이다. self-attention을 통해서 단어가 어느 단어에 의존하는가를 학습하게 된다.

* self-attention의 과정을 알아보자  
  self의 뜻은 query, key, value가 같은 출처(embedding vector)에서 나왔다는 의미이다.   

  **과정)**   
  한 단어에 3가지 벡터를 생성한다. query, key, value.  

  현재 단어의 query 벡터와 모든 단어들의 key 벡터의 내적을 통해서 Score vector를 만든다.    
  이 Score 벡터는 이 단어와 다른 단어의 관계, 디펜던시를 의미할 수 있다.    

  Score 벡터를 노말라이즈 해준다 (이때 기준은 미리 설정한 key 벡터의 크기의 square root)   
  그리고 softmax를 거치게한다. 이 값을 attention rate 라고한다.  
  이 값은 이 단어가 자기자신 혹은 다른단어와 얼마나 interactive 해야하는지를 말한다.  

  각 단어별 attention rate와 value를 weighted sum을 한다.(모든 단어의 attention rate가 곱해진 value벡터를모두 더하는 것)  
  이 값이 self-attention과정의 결과값이 된다.

  **주의점)**  
  query 벡터와 key벡터는 항상 차원이 같다. 내적해야 하기 때문이다.  
  self-attention의 최종값은 value 벡터와 차원이 같다.  

  **왜 잘될까?)**  
  컨볼루션, MLP은(현재 해당 영역의) 인풋값이 동일하다면 아웃풋도 동일하게 고정된다.  
  하지만 transformer는 인풋값이 고정되도 다른 영역의 인풋값들이 달라짐에 따라서  
  아웃풋이 바뀐다. 그러므로 더 플렉시블하고 많은 것을 표현할 수 있다고 볼 수 있다.  

  **한계점)**  
  한번에 계산하기 때문에 처리량(computational cost)이 크다는 점이 있다. RNN 은 반복하는 반면 Transformer는 한번에 처리한다.
  
* Multi-headed attention(MHA)
  
  self-attention을 여러개 만들어서 계산한다. (self-attention = head). 이 기법을 통해서 position(위치, 순서)를 학습하게 된다.  

  과정을 설명해보면  
  1) 늘어난 여러개의 결과값을 concatnate한다.  
  2) 그런데 Encoder의 출력값이 다음 Encoder의 입력값이 되므로 차원을 맞춰야 하는데 concatnate된 값은 차원이 훨씬 큰 상태이다.  
  3) 차원을 줄이기 위한 가중치 행렬를 만들어서 계산해준다.  
  
  **그런데! 실제 구현(코드상)에서는 embedding 된 벡터를 나눠서 각 attention head에 넣어준다.**
  
  
* positional encoding
  입력값으로 original embedding벡터에 더해지는 벡터이다. position vector 구하는법은 생략.
  **왜 필요한가?** transformer에 순서에 대한 고려가 없으므로
  
* 그밖에... encoder내부에서 쓰는 기법
  - layerNorm
  - ResNet 처럼 identity 값 추가

##### 2) Encoder와 Decoder 사이의 어떤 정보를 주고 받는가?   
encoder는 key와 value를 decoder에 보내게 된다.   
추가로 decoder는 만들어야 하는 단어를 입력받고 다음 단어를 생성하도록 학습한다.  

decoder의 작동방법은 encoder와 비슷한데 query는 이전 단어들로 부터 생성하고   
key, value는 encoder로부터 받았다는 것만 다르다.

---

* 트랜스포머는 visual영역에서도 사용한다
* 최근에는 DALL-E 같은 것도 있다. 문장을 주면 이미지 출력
* **Transformer, MAH는 중요한 기법이다. 기억하자**
