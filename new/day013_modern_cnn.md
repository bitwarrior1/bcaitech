## 강의

* Convolution 과 pooling layers 는 feature extraction의 의미를 갖는다

* 반면 fully connected layer 은 decision making(e.g., classification)의 의미를 갖는다

* fully connected layer는 최소화 시키는 추세이다.  (파라미터의 숫자에 달려있지만 일반적으로 그렇다)  

* 학습하고자 하는 모델의 파라미터 숫자가 늘어날수록 학습이 어렵고 generalization performance이 떨어진다. 
  = 학습에서 얻은 결과가 실제 테스트에서의 결과와 차이가 많이 난다는 것을 의미한다.

* 그래서 컨볼루션 레이어를 deep하게 하지만 파라미터 숫자를 줄어는 방식을 이용한다.  
  
* 일반적으로 어떤 NN에서 layer별로 몇개의 파라미터, 전체에 파라미터에 대한 감을 가지고 있어야 한다.

## Convolution

* 커널의 채널 크기는 입력의 채널 크기와 같다.

* 커널의 개수에 따라 출력의 채널 크기가 결정된다.
  커널이 10개라면 출력의 채널 크기가 10이 된다.

* dense layer, MLP layer의 파라미터 숫자가 일반적으로 컨볼루션보다 훨씬크다.  
  컨볼루션의 경우 커널이 입력의 모든 영역에 동일하게 적용되기 때문에 파라미터 수가 상대적으로 적다.   
  그래서 위에서 말했듯이 fully connected layer부분의 파라미터 수를 줄이고   
  컨볼루션 레이어를 깊게 쌓는 시도를 많이 한다.  

* 그러기 위한 시도 중 하나가 1X1 Convolution 이다. 
  깊이를 증가시키며 파라미터숫자를 줄인다. (= Dimension reduction)  
  예) bottleneck 아키텍처

## Modern CNN (이미지 대회에서 나와 해당년도에 1등했던 것들, 최신은 아니나...)

* 파라미터 숫자와 깊이에 주목하면서 봐야한다

#### AlexNet

* 11x11 필터를 쓴다. 커널이 보는 이미지는 커지지만 상재적으로 파라미터의 수는 증가한다.

**아이디어**
1. ReLU 사용 
    1. Non linear function 이지만 linear 속성을 가진다.  
    2. 0이상시 slope가 1이다. 깊은 네트워크에서 그래디언트가 사라지지 않는다.(= overcome the vanishing gradient problem)
  
2. Data Augmentation 사용
    기존 데이터를 이용해 새로운 데이터 만들어서 학습데이터에 추가하는 방법

3. DropOut 사용
    forward 시 일부 뉴런의 값을 0으로 설정해서 꺼버리는 방법

#### VGGNet

**아이디어**
1. 3x3 커널을 사용한다  
    Receptive field(입력값의 크기)을 관점으로 보았을 때,  
    3x3 커널을 두번 적용한 결과값은 입력값에서 5x5 커널으로 추출된 것과 동일하다.
    그런데 파라미터의 숫자는 3x3 더 작다.
    
    * 일반적으로 커널(필터)는 7x7을 넘지 않는다.
 
#### GoogleNet

**아이디어**
1. 1x1 커널을 사용한 컨볼루션  
  채널을 줄이는 작용을 한다. = 파라미터를 줄인다는 의미와 같다.  
  
2. Network in Network 구조를 사용했다.
   
   1. inception block  
      * 입력이 들어오고 퍼졌다가 다시 합쳐짐
      * 하나의 입력이 들어왔을때 **여러개의 receptive field** 를 가지는 필터들을(4개?) 거치고 다시 합쳐짐(=Concatenate). 
      * 이득은? 여러개의 결과들을 합치는 효과도 있다.

   2. inception block 안에서 1x1 컨볼루션 사용
      1x1 convolution can be seen as channel-wise dimension reduction.(=채널방향에서 차원을 줄이는 효과)(필터의 가로세로 == special dimention)
      입력값의 채널을 그대로 유지하려면 커널의 갯수도 동일하게 맞춰야한다.  
      그런데 중간에 1x1 을 끼워넣는다고 파라미터의 수가 줄일 수 있다.  
      
      **예시**  
        1. 3x3 커널   
           * 3 x 3 x 채널수 x 채널수   
        2. 1x1 커널, 채널수 1/4로 감소시키고 다시 확장
            * 1 X 1 X 채널수 x (1/4\*채널수)    
            * 3 x 3 x (1/4\*채널수) x 채널수   
            합치면 1번 보다 더 작다는 것을 알 수 있다
            
### 깊이는 깊어지고 파라미터는 줄어들었다.
AlexNet > VGGNet > GoogLeNet (파라미터수)
GoogLeNet > AlexNet > VGGNet (깊이)

        
### ResNet
   
* Generalization performance : 학습 정확성과 테스트 정확성의 차이를 두고 평가. 차이가 작을수록 좋음  

* 일반적으로 파라미터의 숫자가 늘어나면 생기는 아래와 같은 문제가 있다.

  1) 오버피팅 : 학습데이터에서만 정확성이 증가하고 테스트에서는 감소하는 문제

  2) 학습실패 : 학습과 테스트 모두에서 정확성이 감소하는 문제. (특히 **레이어**가 깊어짐에 따라서 학습데이터에서만 잘 될것이라는 기대와 반대로)

  * 여기서 2번을 해결하기 위해서 ResNet에서는 residual connection ( = identity map = skip connection)을 추가하게 된다.
  * 다시말하면 깊은 레이어에서도 ResNet 구조를 사용해서 잘 학습할 수 있게 되었다
      
* Skip Connection
  convolution layer를 거친 feature map과  
  입력값 자체(혹은 채널,차원을 맞추기위한 하나의 convoultion)의 feature map을 하나로 더하는 방법이다.  
  개념적으로 입력값의 보존하면서 학습을 한다고 볼 수 있다.   
  그라디언트 소실을 방지한다.   

* 히든 레이어안에서 batch normalization도 사용하는데 순서에 대해 이견이 많다.  

* bottlenect architecture이란?  
  1x1 convolution을 앞에 사용해서 파라미터 수를 줄이고
  그 다음에 또 1x1 convolution를 사용해서 원하는 채널,차원을 맞추는데 사용하는 방법

* 보통 Simple Shortcut 사용된다. (Projected Shortcut 보다)

### DenseNet
ResNet에서는 더해주지만(addition) DenseNet은 concatenate 한다.  
concatenate이므로 채널이 기하급수적으로 커진다.(=파라미터수가 증가한다.)  
그래서 중간에 한번씩 채널을 줄인다.(=파라미터수를 줄인다)  

더 구체적으로 보면  
feature map을 concatenate하는 과정을 **Dense Block**
BN-> 1x1 Cov -> 2x2 AvgPooling 을 통해서 차원을 줄이는 과정을 **Transition Block** 이라고한다.  
그래서 2과정을 계속해서 거치는 것이 DenseNet의 방식  

--- 

## 강의 2 - Computer Vision Applications

* Semantic Segmentation  
  이미지의 어느 픽셀이 어떤 라벨에 속하는지 분류하는 것. 자율주행, 운전보조 등에 사용  
  
  * Fully Convolutional Network ( FCN )
  Dense레이어를 삭제하고 convolution으로 대체한다. Convolutionalization. (파라미터 개수는 동일하다.)  
  학습한 네트워크는 더 큰 이미지를 입력값으로 받을 수 있다. output은 마치 Heap Map과 같은 효과.  
  output의 차원이 작아지므로 늘리는 작업이 필요하다.  
  이 중 하나로 Deconvolution이 있다.
  
  * Deconvolution   
  차원을 복원시킬 목적으로 입력에 패딩을 생성후 convolution 진행한다.
  
  
* Detection   
  바운딩 박스안의 이미지가 어떤 라벨에 속하는지 분류하는 것  
  **추가학습**

  * R-CNN
    1. Selective search를 이용해서 이미지에서 수많은 region(바운딩박스)을 뽑아낸다.   
    2. region의 크기를 통일시킨다.   
    3. Alexnet사용하여 region에서 feature map를 뽑아낸다.  
    4. 그리고 svm으로 어떤 이미지인지 분류  

    * bounding box regression  
      바운딩 박스를 어떻게 위치 시킬지에 관한 문제

    * R-CNN의 문제
      디텍션을 위해서 이미지안의 수많은 바운딩박스(>2000)를 다 돌려봐야한다.

  * SPPNET - Spatial Pyramid Pooling   
    SPPNET은 R-CNN과 동일하지만 한번만 돌린다.
    이미지 전체에 대해서 feature map 하나만 만든다.   
    그리고 feautre map에서 바운딩박스에 해당하는 위치에서만 tensor를 가져온다.  

  * Fast R-CNN   
    이미지 전체에 대해서 feature map 하나만 만든다.  
    각각의 region에 대해서 ROI pooling을 통해서 fixed length feature를 뽑는다.   
    그 후 RoI feature vector를 통해서 분류와 바운딩박스 regression을 수행한다.   

  * Faster R-CNN   
    바운딩박스를 뽑아내는 방법도 네트워크를 통해서 학습시킨다.   
    이 네트워크를 Region Proposal Network라고 부른다.   
    그 밖에는 앞의 Fast R-CNN과 동일하다.  

    * Region Proposal Network   
      ( k anchor boxes )   
      어떤 크기의 물체가 있을 지 미리 결정해둔다.  
      9 - 3개의 사이즈와 3개의 비율  
      4 - 바운딩 박스 크기 파라미터  
      2 - 분류파라미터(whether to use it or not)  

  * YOLO   
  매우빠름.   
  바운딩박스 영역마다 샘플링해서 처리하지 않고 한번에 작동하는 방식  

* 테스트  
Semantic Segmentation 과 Detection 에서 해결하려는 문제   
문제를 풀기위해 활용한 방법들   
발전 흐름

