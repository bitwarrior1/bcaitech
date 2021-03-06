## 강의 내용

![Preprocessing](https://user-images.githubusercontent.com/28282381/114577454-f7c81d00-9cb6-11eb-96de-fe7dde5be0d0.png)


### Quantile transformation - 이해불가

* Scaling의 한가지 방법, scale + distribution 둘다 변경시킨다.
* 현재 분포를 다른 분포로 매핑하는 방법이다.  
* 예시코드 3강에서 sklearn을 이용해 사용한다.
* It may distort linear correlations between variables measured at the same scale (sklearn)

#### 이해한 내용
기본 개념은 **Universality of the uniform distribution** 인 것 같다. **균등분포의 보편성**이라는 것 같다.   
이것에서 파생된 개념, 혹은 같은 개념인
* quantile transformation
* probability integral transform
* inverse transform sampling   
  
가 있다.  

##### Universality of the uniform distribution
우측으로 증가하는 조건이 주어지면(조건이 몇가지 있다)   
cumulative distribution function(CDF)를 uniform distribution 형태로 바꿀수 있다.      
또한 반대로 uniform distribution은 어떤 형태의 분포로든 변경 가능하다는 내용이다.   

관련내용을 찾아봤는데, 어떤 과정을 통해서 이게 가능한지 이해가 되지는 않았다.

일단, 

1. 누적분포함수(CDF)는 input값으로 **x**를 받는데 output은 확률변수 X가 input값 **x**보다 낮거나 같은 값이 나올 **확률**이다.
2. 누적분포함수(CDF)의 역함수는 Quantile function(=percent-point function)이다.
3. 누적분포함수 F를 따르는 확률변수 X를 다시 자신의 분포함수 F를 사용해서 분포를 Y를 구하는데 이것이 Uniform[0,1] 이라는 사실을 이용.
  
정도만 알았다

------
이해없이 결과만 보면 어쨌든 우리의 현재 feature 분포를 uniform으로 전환한 뒤,  
normal distribution으로 다시 변경한다. (sklearn의 parameter값에 따라 다르다. uniform / normal)
