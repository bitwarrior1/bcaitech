# 강의

### 컴퓨터 비전
* 컴퓨터 비전은 빠르게 발전하고 변화하는 분이야다.(모든 학문분야를 고려해도)
* 과거의 방법들은 그 자체보다는 현재 나오고 있는 것들을 이해하기 위함이다.
* 논문등으로 혼자서도 정보를 습득할수 있는 능력이 필요하다.

### Inverse Graphics
* 컴퓨터 비전은 이미지로부터 본질을 파악(정보를 얻는)하는것이며,
* graphics, rendering과 반대되는 것이라고 볼수 있다. Inverse Graphics(Rendering)라고 불리기도 한다.

### Human Visual
* 우리 인간의 시각도 불완전하며 특정상황에서는 익숙한 장면으로 보완하여 해석하기도 한다.(=특정패턴) ex)뒤집혀진 얼굴

### CNN
* 분류문제에서 Fully Connected Layer로 학습된 가중치, W를 이미지화 해보면 해당 레이블의 평균적인 모습을 보인다.
* 평균적인 모습을 벗어난 사진들, Cropped사진 같은 것은 분류하지 못한다.

* **CNN**
* CNN은 지역적인 feature를 뽑아내는 형태로 작동한다.
* 파라미터의 수도 훨씬 적다. = 오버피팅 감소
* 비전에서 CNN를 사용하는 기본적인 구조는
    1. 이미지에서 특징들을 뽑아내는 backbone과 (=base)
    2. 뽑아낸 feature map으로 task를 수행하는 head부분이 있다.

### Augmentation

* 이미지들 대부분은 사람에 의해서 촬영된 것이고 실제 보이는 세상과는 다른 어떤 편향을 갖게 된다.
* 이 편향을 해소하는 관점으로도 Augmentation을 실행한다고도 볼 수 있다.
* 라이브러리에서 지원
* 최고의 Augmentation 조합을 찾는 **RandAugment**

##### 기준
* Brightness
* Rotate
* Flip
* Affine transformation : 세점...
* cutmix : label도 이산적이지 않고 %로 mix
* 등등...

### Leveraging
* 큰 비용이 드는 labeling 작업을 어느정도 해결
* pre-trained 된 모델 가져와 사용
* pre-trained 모델의 마지막 fc레이어만 task에 맞게 변경
   
1. Transfer Learning: 기존 파라미터는 freeze, 추가한 레이어의 파라미터만 학습한다
2. fine-tuning: 기존 파라미터는 조금만 학습, 추가한 레이어의 크게 학습

### Knowledge Distillation
* Passing what model learned to 'another' smaller model (Teacher-Student learning)
* 모델 compression로도 볼 수 있다.
* 사용예
    1. 같은 Input에 대해 teacher와 student의 Output의 차이를 줄인다. KL div. Loss를 이용하고 Student를 수정. label이 필요없으므로 Unsupervised learning.   
    * Teacher와 Student의 차이를 Distillation Loss라고 한다
    2. Distillation Loss와 label된 데이터를 이용해서 구한 Loss, 이 두 Loss를 이용해서 Student학습
    * "soft" prediction : 실수를 갖는 값
    * Temperature

    3. semi-supervised learning with psedo-label
        * 한 model에서 생성한 출력을 unlabel데이터의 psedo-label로 사용.
        * 이 데이터를 합쳐서 다시 model 학습
    
### 위 세가지 기법을 이용한 self-training
1. Teacher 모델을 학습시킨다.
2. Teacher 모델로 unlabeled data에 psedo-label설정해 준다.
3. 이 데이터를 합쳐서 RandAugment를 진행하고, Student 모델ㅇ,ㄹ 학습한다.
4. Student모델을 Teacher모델로 정하고 다시 반복한다(2번으로)
* 모델이 점점 커진다.

        
