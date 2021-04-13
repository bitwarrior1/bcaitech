### ROC

* ROC curve (receiver operating characteristic curve)
* This curve plots two parameters:
  1) True Positive Rate
  2) False Positive Rate
 
* True Positive Rate (TPR)
  * 실제 Positive 중에서 얼마나 positive로 예측했는가.
  * 맞는예측 비율이다
  * Recall과 동일  
  <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{TP}{TP&space;&plus;&space;FN}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{TP}{TP&space;&plus;&space;FN}" title="\frac{TP}{TP + FN}" /></a>
  
* False Positive Rate (FPR)
  * 실제 Negative 중에서 얼마나 positive로 예측했는가
  * 틀린예측 이다  
  <a href="https://www.codecogs.com/eqnedit.php?latex=\frac{FP}{FP&space;&plus;&space;TN}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\frac{FP}{FP&space;&plus;&space;TN}" title="\frac{FP}{FP + TN}" /></a>
  
* 일반적으로 TPR은 높아야지, FPR은 낮아야지 좋아보인다.

* 이진 분류시 threshold를 얼마나로 설정했는냐에 따라 positive, false 판단이 변하는데 이 때 TPR, FPR의 값도 변한다.
* threshold의 변화로 인한 TPR, FPR을 ROC curve로 표현한다
* 곡선의 한점을 하나의 threshold에서 TPR, FPR의 값이라고 생각해 볼 수있다.

* 예시 그래프에서 임계값이 올라가면 좌측곡선 지점으로 (TPR, FPR 둘다 감소)
* 임계값이 내려가면 우측곡선 지점으로 보인다. (TPR, FPR 둘다 증가)

### AUC

* Area under the "ROC" curve 
* ROC 커브 곡선의 크기
* 1에 가까워질수록 좋다
* 예측이 0과 1, 두 지점에 가까워지면서 올바르다면 높은 AUC를 가질 것이다.
* scale-invariance 스케일불변 : 절대적인 값이 아니라 모델이 얼마나 잘 예측하는지를 평가
* classification-threshold-invariant: 어떤 threshold를 가지는지 상관없이 평가
