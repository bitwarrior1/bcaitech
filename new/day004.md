# 강의 

* 클래스  

  * 속성, Attribute
  * 행동, Action  
  
  클래스는 개념적으로 위 2가지로 이루어짐


* mangling 
  * 함수이름 앞뒤로 언더스코어 두개가 있다면 특수 예약함수이다.  
  ```ex) __init__```  

  * 변수이름 앞에 언더스코어 두개가 있다면 private 변수이다.  
  ```ex) self.__name```

* first class function  
파이썬에서 함수는 first class citizens이다.  
이 뜻은 함수가 변수에 할당되거나 인자로 사용될 수 있음을 말한다.

* inner function (= nested function)  
함수안에 있는 함수 

* clouser  
inner function을 return하여 사용하는 방식을 뜻한다.

  ```python
  def outer(text):
      var = "remember me"
      def inner(a):
          print(a, text, var)

      return inner

  f1 = outer("hi")
  f1("123") # 123 hi remember me
  ```  
  outer함수가 inner함수를 리턴후 종료되었지만  
  inner함수 바깥에 있는 변수들(text, var)이 사라지지 않고 사용할 수 있다.
  
 
* decorator  
다른 함수를 장식해주는(?) 함수를 말한다.  
장식되는 함수 기능 자체는 변화가 없다.
  ```python
  def outer(func): # << decorator
    
      def inner():
          print("i'll decorate you")
          func()
      
      return inner
   
  def me():
      print("me")
    
  decorated_me = outer(me)
  decorated_me()
  
  # 출력
  # i'll decorate you
  # me
  ```  
  
  파라미터를 사용한다면 inner함수가 받도록 해야한다. (리턴되는 것은 inner이므로)
  ```python
  def outer(func): # << decorator
    
      def inner(x): # << *args 가능
          print("i'll decorate you")
          func(x)
      
      return inner
   
  def me(name):
      print(name)
      
  decorated_me = outer(me)
  decorated_me("me")
  # 출력
  # i'll decorate you
  # me
  ```  
    
  데코레이터를 @를 이용해 간단히 쓸 수 있다
  ```python
  @outer  
  def me():
      print("me")
    
  me() # 출력값 동일
  ```
  
  @를 이용한 데코레이터는 여러번 가능하다
  ```python
  @outer1
  @outer2
  def me():
      print("me")
  ```
  
* 패키지 파일 구성  
  
  * \_\_init\_\_.py  
  패키지임을 나타내는 파일 (파이썬 3.3 이상에서는 없어도 된다)  
  \_\_all\_\_ 변수에 사용할 모듈을 작성한다.
  
  * \_\_main\_\_.py  
  모듈을 실행시킬 경우가 있다면 작성
  
  
  
  
  
