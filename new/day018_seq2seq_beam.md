# 강의

### 

1. Sequence to Sequence

sequence 형태의 input & output를 갖는다. not share parameters  
각 RNN 모델은 LSTM를 사용  
마지막 encoder의 hidden state는 decoder의 처음(0번째) hidden state로 쓴다.  
decoder의 첫번째 인풋은 SOS토큰, 그리고 EOS 가 나올때가지 단어 생성을 반복한다.  

2. Attention  

문제 : LSTM이 먼거리의 정보를 유실한다.

attention value 구하는 과정 : 
디코더의 hidden state와 모든 인코더의 hidden state의 내적 -> scalar 값 -> 합쳐서 attention socre -> softmax를 통과시켜서 attention distribution   
-> 모든 인코더 hidden state에 가중합 (각 인코더 h에 attention distribution을 곱하고 모두 각 인코더 h들을 모두 더한다) -> attetion value (= context vector )  

* dot, general, concat 과 같은 방법들이 있다. 위의 설명은 **dot**방법이다.

이후 출력값 구하는 과정:
attention value와 디코더의 hiddent state의 concatenate -> linear 변환 -> softmax -> 결과

* decoder hidden state의 2가지 역할
1. 해당 time step의 아웃풋 계산시
2. attetion score 구할시

* decorder 학습시에는 ground-truth 제공하기도 한다. (= 이 방법을 **teacher forcing** 이라고 한다),
  당연히 inference에는 이전 아웃풋사용

### beam search

지금까지는 greedy decoding방식으로 가장높은 확률의 단어 선택하였다.  
이전 output을 변경할 수 없다.  

* 해결법
  매 스텝마다 모든 단어에 대한 확률을 구한다.
  **너무 많다!**
  
* 타협점
  높은 확률순으로 k개 단어에 대한 확률을 매번 구한다.  
  구체적으로,        
  스텝이 진행될때마다 이전 단어에 대하여 (조건부)  
  k개의 단어 확률을 구하고 ( 구하는 단어의 수는 처음을 제외하고 k^2 (이전 단어 * k 개) )  
  이전 확률과 로그 덧셈을 적용해 확률을 계산한다  
  그 중에서 가장높은 k개 단어 선택한다  

* 마지막까지 decoding  후 확률이 가장 높은 세트를 선택.    
  여기서 매 디코딩후 확률을 hypothesis 라고 부른다.   
  k는 beam size라고 부른다. 보통 5~10개   

* 계산시 log로 바꾸어서 계산한다

* hypothesis간 길이 차이
  각 hypothesis는 **다른 시점에서** \<EOS\>토큰을 생성해낼 수 있다.  
  joint로 진행되므로 대개 더 짧은 길이가 확률이 높다는 문제가 있으므로   
  최종 계산시에 추가로 단어당 평균으로 만드는 normalize를 진행한다.  

* 종료시점 설정
  어느 time step까지 진행할 지를 미리 정한다.
  또는 EOS에 도달한 hypothesis의 개수를 기준으로 종료한다.

* 최종결과값은 가장 확률높은 hypothesis



### BLEU score

자연어 생성모델의 품질, 결과의 정확도를 평가하는 척도인 bleu score

고정된 위치로 단어의 일치를 판단하면 제대로 평가하지 못한다.   
예)   
Oh I love you  
I  love you  
0% 정확성   

* precision 과 recall, 그리고 k-gram을 사용해서 BLEU를 구한다.

* precision은 예측한 단어들 중에서 얼마나 맞는 값들이 나왔는가가 기준,  
* recall은 Reference(=target) 단어들중에서 얼마나 맞는 값들이 나왔는가 가 기준이 된다.

* precision과 recall은 순서에 상관없이 계산하는 문제가 있어서.  
  k-gram, 연속된 단어(k개)가 모두 맞아야지 인정하는 기법을 사용한다.
  1~4 -gram을 구해서 평균을 내는 방식을 사용한다.

----

### 기타

* 산술평균 >= 기하평균 >= 조화평균(=작은쪽에 집중)
                        
* bidirectional -> 양방향 concat후 linear변환을 한번 거치는... 방식

