# 강의

### RNN

[입력]
1. 매 타임스텝마다의 인풋값
2. 이전 타임스텝에서의 히든state

[출력]
1. 매 타임스텝마다의 아웃풋 (매번 필요할때도 있고 최종적으로만 필요할때도 있다)
2. 다음 스텝으로 보내는 히든state

* 매번 사용하는 파라미터는 동일  
* RNN은 input과 ouput이 sequence, non sequence 일 때 둘다 다룰 수 있다.  
  다양한구조를 갖는다.
  
---

#### Character level language model

* 한글자 단위의 모델
* 추론시 (=테스트) 한 time step의 출력값을 다음 입력값으로 사용하게 된다.
* RNN BACKPROPAGATION  
  * 일부 시퀀스로 나누어서 학습을 진행하기도 한다. 한번에 학습하기에는 너무커서!, Truncation이라 한다
  * 바닐라 RNN 에서의 vanishing/exploding gradient 문제가 있다.  
    LSTM에서 어느정도 해결된다.

--- 

### LSTM

* cell state는 보다 완전한 정보라고 볼 수 있다.
* hidden state는 cell state를 한번 가공하여 현재 타입스텝에서 노출할 정보만을 남긴 것. 어떤 필터를 한번 더 거친 것이라고 볼 수 있다.
* cell state는 vanishing/explosion gradient 되지 않는다. (덧셈기반이라서)
  
* 게이트
<img src="https://github.com/bitwarrior1/bcaitech/blob/main/new/img/rnn_gates.png" width="600" height="450" />

* forget gate  
  이전 cell state에 element-wise 곱( X )해준다.   
  곱하는 수는 이전 hidden state와 현재time-step의 가중치계산 후 시그모이드 함수를 거친 수인데 1보다 작아서  
  값을 낮추는 역할을 하는데 이것을 forget이라고 생각할 수 있다.
  
* input gate + gate gate  
  이전 cell state에 정보를 더해준다.

* output gate   
  현재 cell state와 out gate결과를 이용해서 hidden state를 만든다.  
  cell state에서 현재 필요한 결과만 필터링한 것으로 생각 할 수 있다.  
  현재 layer의 output 은 필요할 시 내보낸다.


### GRU

* LSTM를 경량화하였다.
* LSTM 대비 적은 메모리, 빠른 계산 시간이 장점이다.
* Cell state와 Hidden state를 하나로 취급. ( = LSTM의 Cell state에 가깝다)

---

#### Language Modeling

* 다음 단어를 예측해서 아웃풋으로 내놓는다.  

* PackedSequence  
  padding이 적용안된 순서부터 계산되도록 정렬해서 pack
  padding 부분은 굳이 계산하지 않도록 하는 방법
  
* 학습시에는 time step의 인풋을 이전 layer에서 가져오지 않고 직접 target 값을 넣어주기도 한다.

* RNN 학습은 양방향(bidirectional)으로도 가능하다.
