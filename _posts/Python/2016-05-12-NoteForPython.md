---
layout: post
title:  "Python 학습내용 기록"
date:   2016-05-12 12:00:00
categories: Python
lastmod: 2016-05-29 12:00:00
---

![Python](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/260px-Python_logo_and_wordmark.svg.png)  
( 출처 : [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)) )

Python 학습을 했습니다. 실제로 사용하면서 무엇을 만들어본 것은 아니고요.  
헷갈리기 쉬운 기본적인 문법, 주의해야할 사항 그리고 독특한 사항들 위주로 기록해봅니다.  

<!--more-->

# 환경구성

## Python 3.x
  * [Install Python](https//www.python.org)
  * PATH 설정

## IDE
  * [Install PyCharm](https://www.jetbrains.com/pycharm/download)

# Variable
  * 자료형에 대한 선언이 필요없다. 참조하는 객체의 자료형에 따라 자동으로 처리된다.
  * Python 에서 객체가 아닌 자료형이 존재하지 않기 때문에 가능하다.

# Data Structure

## Integer
  * 정수도 객체다.

### No Limit
  * python 3.x 에서 Integer 는 한계 값이 없다.
  * 다만, sys.maxsize 값이 제공되는데 list, index 와 같은 데이터 구조의 한계 값으로 사용된다.
  * sys.maxsize 는 python 2.x의 sys.maxint 와 값이 같다.

  > The sys.maxint constant was removed, since there is no longer a limit to the value of integers. However, sys.maxsize can be used as an integer larger than any practical list or string index. It conforms to the implementation’s “natural” integer size and is typically the same as sys.maxint in previous releases on the same platform (assuming the same build options).  
  > 출처 : [python](https://docs.python.org/3.1/whatsnew/3.0.html#integers)

~~~python
import sys
x = sys.maxsize # python 2.x 에서의 integer 최대 값
print("Bit Length of {} : {}".format(x, x.bit_length()))
# 결과 : Bit Length of 9223372036854775807 : 63

y = x<<99 # left bit shift 연산자
print("Bit Length of {} : {}".format(y, y.bit_length()))
# 결과 : Bit Length of 5846006549323611672180914030751017377875378569216 : 162

z = x<<100
print("Bit Length of {} : {}".format(z, z.bit_length()))
# 결과 : Bit Length of 11692013098647223344361828061502034755750757138432 : 163

print(z/y)
# 결과 : 2.0
~~~

## Boolean
  * 별다른 내용없음

## Float
  * 별다른 내용없음

## Sequence
  * 순서가 있는 객체들의 모음을 의미한다.
  * in, not in 연산이 가능 
  * indexing, slice 연산 : list[2], list[-1], list[1:3], list[1,-1], list[1:], list[:-1]
  * 구현체로는 String, List, Tuple 등 이 있다.

## String
  * Immutable : string 의 구성물을 추가, 변경, 삭제가 불가능하다.

### Concatenation
  * Immutable 이기 때문에 ```+``` 연산자로 문자열 조합을 수행하면 매번 string 을 새로 생성한다.
  * 매번 메모리를 새로 할당하기 때문에 비용이 크다.

    ~~~python
      import time

      loop_size = 1000000
      test_string = "0123456789"
      start_time = time.time()

      # '+' 연산자를 이용한 방법
      for i in range(1, loop_size):
          result_string += test_string

      elapsed_time = time.time() - start_time
      #print(result_string)
      print("Elapsed {0:.02f} when loop_size = {:,}".format(elapsed_time, loop_size))
      # Elapsed 13.75 when loop_size = 1,000,000
    ~~~

  * 많은 횟수의 반복적인 문자열 조합을 수행할 경우 list 를 이용하는 방법이 추천된다.

    ~~~python
    import time

    loop_size = 1000000
    test_string = "0123456789"
    start_time = time.time()

    # List 를 이용한 방법
    str_list = []
    for i in range(1, loop_size):
	      str_list.append(test_string)
    result_string = ''.join(str_list)

    elapsed_time = time.time()-start_time
    #print(result_string)
    print("Elapsed {0:.02f} when loop_size = {:,}".format(elapsed_time, loop_size))
    # Elapsed 0.38 when loop_size = 1,000,000
    ~~~

  * 동일하게 list 를 사용하는 방법이지만 아래 방법이 더 빠르다.

    ~~~python
    import time

    loop_size = 1000000
    test_string = "0123456789"
    start_time = time.time()

    # List comprehensions 를 이용한 방법
    result_string =''.join([test_string for num in range(1,loop_size)])

    elapsed_time = time.time()-start_time
    #print(result_string)
    print("Elapsed {0:.02f} when loop_size = {:,}".format(elapsed_time, loop_size))
    #Elapsed 0.18 when loop_size = 1,000,000
    ~~~

#### References
  * [Python 에서 효율적인 String Concatenation 방법](http://blog.leekchan.com/post/19062594439/python%EC%97%90%EC%84%9C-%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9D%B8-string-concatenation-%EB%B0%A9%EB%B2%95)

## List
  * 선언 : ```[]```
  * 모든 형태의 객체를 담을 수 있다.

## Tuple
  * 선언 : ```()```
  * Immutable : tuple 의 구성물을 추가, 변경, 삭제가 불가능하다.
  * 모든 형태의 객체를 담을 수 있다.
  * Tuple 은 한번의 대입연산자로 여러개의 변수에 나눠담을 수 있다.

~~~python
def get_person():
    return ["Dveamer","Korean","Dveamer@gmail.com"]

name, national, mail = get_person()
print(name, national, mail)
~~~

## Dictionary
  * 선언 : ```{ key1 : value1, key2 : value2 }```
  * 모든 형태의 객체를 담을 수 있다.

## Set
  * 선언 : ```set()```
  * 모든 형태의 객체를 담을 수 있다.

## Frozenset
  * 선언 : ```frozenset()```
  * Immutable : frozenset 의 구성물을 추가, 변경, 삭제가 불가능하다.
  * 모든 형태의 객체를 담을 수 있다.

# Operator
  * ``` num++ ``` 연산자가 존재하지 않습니다.
  * ``` / ``` 연산자는 나눗셈 결과를 소수점 형태로 반환한다.
  * ``` // ``` 연산자는 나눗셈의 몫을 반환한다.
  * ``` divmod(a, b) ``` 는 나누셈의 몫과 나머지를 tuple 로 반환한다.
  * ``` ** ``` 연산자는 power 연산자이다. ``` 3**4 == 3*3*3*3 ```

# Statement
  * ```{}``` 와 같은 block 표현이 없습니다. 대신 tab(혹은 blink) 간격으로 인식합니다.
  * 하나의 statement 가 여러 줄로 나뉘게 될 때 ```\``` 표시를 통해서 연결가능하다.

# Control Statements

## If Statement

~~~python
value = 1
if value == 1:
    print("value : 1")
elif value == 2:
    print("value : 2")
else:
    print("value is not 1 and 2")
print("value is {}".format(value))
~~~

  * elif 라는 예약어가 독특합니다.

## While Loop Statement

~~~python
for index in range(1,5):
    print(index)
~~~

## For Loop Statement
  
~~~python
index = 0
while index < 5:
    print(index)
    index += 1
~~~

## Else Statement Of Loop

  * While, For 루프도 else 문법을 갖는 점이 독특합니다.
  * Loop 의 조건절이 false 일 때 수행됩니다.
  * break 문과 함께 사용될 때 의미가 있을 것으로 보여집니다.

~~~python
my_year = 1984
my_month = 2
my_day = 29

chance = 3
for index in range(1,chance):
    year = int(input("년 : "))
    month = int(input("월 : "))
    day = int(input("일 : "))

    if my_year == year and my_month == month and my_day == day:
	    print("맞췃습니다.")
	    break
    else:
        print("{}회 틀렸습니다. {} 회 기회가 남았습니다.".format(index, chance - index))
else:
    print("모든 기회를 소진하셨습니다.") # 답을 맞출 경우 break 문으로 탈출하기 때문에 수행되지 않습니다.
~~~

# Function

~~~python
def say_hello():
    print("반갑습니다.")

say_hello()
~~~

  * Overloading 이 불가능하다. 대신, Default Parameter 기능으로 처리가능하다.
  * Python 의 function 은 First-class function 이기 때문에 객체로 다룰 수 있다.
    - 런타임시 함수 생성 가능
    - 변수에 할당, 전달 가능

## Local & Global Variable

  * Function 밖에서 정의될 경우 global variable
  * Function 내에서 정의될 경우 local variable

~~~python
name = "Dveamer 씨" # 전역 변수
msg = "안녕하세요." # 전역 변수

def say_hello_local(name, msg):
  print(name, msg)
  name = "Local 씨" # 지역 변수
  print(name, msg)

say_hello_local(name, msg)
print(name)

# 결과
# Dveamer 씨 안녕하세요.
# Local 씨 안녕하세요.
# Dveamer 씨
~~~

  * Function 내에서 global 변수에 접근하기 위해서는 선언이 필요하다.

    ~~~python
    name = "Dveamer 씨" # 전역 변수
    msg = "안녕하세요." # 전역 변수

    def say_hello_global(msg):
        global name # global 변수 접근을 위한 선언
        print(name, msg)
        name = "Global 씨" # 전역 변수
        print(name, msg)

    say_hello_global(msg)
    print(name)

    # 결과
    # Dveamer 씨 안녕하세요.
    # Global 씨 안녕하세요.
    # Global 씨
    ~~~

## Default Parameter

  * 값이 넘어오지 않은 파라미터에 대해 default 값을 제공한다.
  * default 값을 가지지 않은 파리미터는 가진 파라미터 뒤에 위치할 수 없다.

~~~python
def say_hello_default(name="Default 씨", msg="오랜만입니다."):
    print(name, msg)

say_hello_default()
say_hello_default("Dveamer 씨")
say_hello_default("Dveamer 씨", "안녕하세요.")

# 결과
# Default 씨 오랜만입니다.
# Dveamer 씨 오랜만입니다.
# Dveamer 씨 안녕하세요.
~~~

  * default 값은 function 이 생성되는 시점에 함께 생성된다.
  * default 값이 객체인 경우는 매번 최초에 생성된 객체가 참조되고 객체는 앞서 발생한 결과를 담고 있다.

    ~~~python
    def append(x, items=[]):
        items.append(x)
        return items

    print(append(1))
    print(append(2))
    print(append(3))

    # 결과
    # [1]
    # [1, 2]
    # [1, 2, 3]
    ~~~

  * 매번 새로운 객체를 default 값으로 받고 싶다면 아래와 같이 진행하면 된다.

    ~~~python
    def append(x, items=None):
        if items is None:
            items = []
        items.append(x)
        return items

    print(append(1))
    print(append(2))
    print(append(3))

    # 결과
    # [1]
    # [2]
    # [3]
    ~~~

## Keyword Parameter
  
~~~python
def func_keyword_parameter(x, y=5, z=10):
    print("X={}, Y={}, Z={}".format(x, y, z))

func_keyword_parameter(1, z=20)
~~~

* Function 을 호출시 파라미터 keyword 를 지정해 줄 수 있다.

## Multiple Parameter

~~~python
def compute_total(initial, *args, **key_args):
    count = initial
    for number in args:
        count += number
    for key in key_args:
        count += key_args[key]
    return count

total = compute_total(10, 1, 2, 3, vegetables=50, fruits=100)
print("합계 : {}".format(total))
~~~

  * args 는 tuple 자료형의 가변인수로 사용되었다.
  * key_args 는 dictionary 자료형의 가변인수로 사용되었다.
  * 가변인수는 인수들 중 가장 마지막에 위치해야한다.
  * args 보다 key_args 가 더 마지막에 위치해야한다.

## Documentation String

~~~python
def max(x, y):
    '''
     :param x: 첫번째 숫자
     :param y: 두번째 숫자
     :return: 두 숫자 중 더 큰 숫자를 반환함
    '''
    if x > y:
        return x
    else:
        return y

result = max(10, 20)
print(result)
print(max.__doc__)

# 결과
#20
#
#   :param x: 첫번째 숫자
#   :param y: 두번째 숫자
#   :return: 두 숫자 중 더 큰 숫자를 반환함
~~~

  * Function 에 대한 주석을 출력시킨다.

## Function 과 Method 의 차이
  * Method 는 class 내에 정의되고 해당 Object 를 통해 사용 가능하다.
  * Function 은 전역에서 사용 가능하다.

# Module
  * 하나의 File 이 하나의 모듈이다.

## Import

~~~python
from io.dveamer.modules import my_module

my_module.func()
~~~

  * import 하면 해당 module 의 전역변수와 function 에 접근 가능하다.
  * import 시 해당 모듈의 실행코드도 1회 실행된다.
  * 1개의 module 을 직접적으로 import 할 경우 __init__.py 파일 설정 없이도 가능하다.

## Multiple Import

~~~python
# io.dveamer.modules.__init__py 파일
__all__ = ("my_module", "your_module") # namespace of module
~~~

~~~python
from io.dveamer.modules import *

my_module.func()
your_module.func()
~~~

  * 특정 package 의 여러 module 을 import 할 때는 __init__.py 파일에 설정이 필요하다.
  * __init__.py 에 설정되지 않은 module 은 package 단위로 import 할 때 제외된다.
  * Library 형태로 제공할 때 interface 목적으로 사용되는 모듈만 __init__.py 에 설정하면 사용되지 않는 모듈들도 import 되는 것을 막을 것 같다.
  * 그렇다고 외부 package 에서 설정된 파일에 접근할 수 있는 권한이 없는 것은 아니다. Direct import 를 하면 접근 가능하다.

# Class

~~~python
class Account:
    __base_rate = 0.01 # Private Class Field : 모든 instance 에 대해 하나의 메모리 영역 할당 됨.

    def __init__(self, name, balance): # Initializing Method
        self.__name = name # Private Instance Field
        self.__balance = balance
        self.__rate = 0

    def compute(self): # Public Method
        self.__balance  += self.interest # 마치 필드를 사용하듯이 self.interest() 메소드를 사용함

    def change_rate(self, rate): # Public Method
        self.__rate = self.__correct_rate(rate)

    def display(self): # Public Method
        print("{} 씨의 계좌잔액 : \\ {:,} ".format(self.__name, self.__balance))

    def display_next_balance(self): # Public Method
        print("{} 씨의 다음달 계좌 예상잔액 : \\ {:,} ".format(self.__name, self.__balance + self.interest ))

    @staticmethod
    def __correct_rate(change): # Private Static Method
        if change < 0:
            return 0
        return change

    @classmethod
    def change_base_rate(cls, base_rate): # 클래스 필드에만 접근가능한 메소드
        cls.__base_rate = cls.__correct_rate(base_rate)

    @property
    def interest(self): # 필드처럼 사용되는 메소드
        return self.__balance * self.__base_rate + self.__balance * self.__rate


account = Account("Dveamer", 100000)
account.display()
account.display_next_balance()

account.change_base_rate(0.05)
account.display_next_balance()

account.change_rate(0.1)
account.display_next_balance()

account.compute()
account.display()
~~~

## Public, Protected, Private
  * Private 을 정의할 때는 field / method 명 앞에 '__' 을 붙인다.
  * Protected 를 정의할 때는 field / method 명 앞에 '_' 를 붙인다.
  * 붙이지 않으면 public 으로 정의된다.

## self
  * instance 를 의미한다.
  * 일반 메소드를 선언시, self 는파라미터로 가장 앞에 적어줘야한다. 호출시에는 파라미터에 기입하지 않는다.

## Method
  * class 내에 정의된 function 은 method 라고 부르며 instance 혹은 class 를 통해서 호출 가능하다.

## cls
  * class 를 의미한다.

### Class Field
  * @classmethod 데코레이터를 지정함으로써 선언할수있다.
  * clz 가진 field 는 해당 class 내에서 유일한 자원이며 모든 instance 가 공유한다.

### Class Method
  * class 멤버로만 접근 가능하며 instance 에 영향을 주지 못한다. 즉, class field, method 로만 접근할 수 있다.

## Static Method
  * @staticmethod 데코레이터를 지정함으로써 선언할수있다.
  * 전역함수(global function)로 만드는 것과 객체의 정적 메소드로 만드는 것은 사용하는 과정에서는 거의 차이가 없다.
  * 다만, 특정 객체에 밀접하게 관련된 내용이라면 해당 객체내에 정적 메소드로 작성하는 것이 의미를 명확하게 만든다.

## property
  * @property 데코레이터를 지정함으로써 선언할수있다.
  * 메소드를 필드처럼 사용하게 해준다.
  * 매번 연산이 일어난다.

# Inheritance

~~~python
class Employee:
    def __init__(self, name, address, email):
        self.name = name
        self.address = address
        self.email = email

    def display(self):
        print("이름 : {} \n주소 : {} \n이메일 : {}".format(self.name, self.address, self.email))


class RegularEmployee(Employee):
    def __init__(self, name, address, email, pay):
        super().__init__(name, address, email)
        self.pay = pay

    def paycheck(self):
        return self.pay

    def display(self):
        super().display()
        print("급여 : {}".format(self.paycheck()))


employee = RegularEmployee("Dveamer", "Seoul", "dveaemer@gmail.com", 100)
employee.display()
~~~

## Term
  * base class : parent class / super class
  * derived class : child class / subscribe class

  * generalization : class 들간의 공통사항들을 가지고 base class 를 뽑아내는 작업
  * specialization : base class 을 기반으로 특수한 derived class 를 만들어가는 작업

# Polymorphism

~~~python
from abc import ABCMeta, abstractmethod


class Mover(metaclass=ABCMeta):
    @abstractmethod
    def move(self):
        pass


class WheelChair(Mover):
    def __init__(self):
        self.name = "WheelChair"

    def move(self):
        print("{} 를 이동시킵니다.".format(self.name))


class OfficeChair(Mover):
    def __init__(self):
        self.name = "OfficeChair"

    def move(self):
        print("{} 를 이동시킵니다.".format(self.name))

    def lean(self):
        print("{} 의 등받이를 기울였습니다.".format(self.name))


chairs = [WheelChair(), OfficeChair()]
for chair in chairs:
    chair.move()
~~~

  * Metaclass 는 Java 의 interface 과 가장 유사한 개념이다.
  * @abstractmethod 선언해주면 구현체에서 구현을 안할시 에러 메시지를 준다.

# Multi Inheritance
  * 다중 상속도 가능하다.
  * [다이아몬드 문제](http://zetawiki.com/wiki/%EB%8B%A4%EC%9D%B4%EC%95%84%EB%AA%AC%EB%93%9C_%EB%AC%B8%EC%A0%9C) 를 해결하기 위해 python 3.x 에서는 ```super()``` 라는 기능이 존재하지만 굳이 일반 클래스에 대해서 다중상속을 할 필요가 있을까?
  * Metaclass 에 대한 다중 상속은 유용하게 사용가능하다.

~~~python
from abc import ABCMeta, abstractmethod


class Mover(metaclass=ABCMeta):
    @abstractmethod
    def move(self):
        pass


class Prop(metaclass=ABCMeta):
    @abstractmethod
    def lean(self):
        pass


class WheelChair(Mover):
    def __init__(self):
        self.name = "WheelChair"

    def move(self):
        print("{} 를 이동시킵니다.".format(self.name))


class OfficeChair(Mover, Prop): # 다중상속
    def __init__(self):
        self.name = "OfficeChair"

    def move(self):
        print("{} 를 이동시킵니다.".format(self.name))

    def lean(self):
        print("{} 의 등받이를 기울였습니다.".format(self.name))


chairs = [WheelChair(), OfficeChair()]
for chair in chairs:
    chair.move()

chairs[1].lean()
~~~

# Operator Overloading

~~~python
class Complex:
    def __init__(self, real, imaginary):  # Overloading for __init__
        self.real = real
        self.imaginary = imaginary

    def __add__(self, other): # Overloading for '+' operator
        return Complex(self.real + other.real, self.imaginary + other.imaginary)

    def __sub__(self,other): # Overloading for '-' operator
        return Complex(self.real - other.real, self.imaginary - other.imaginary)

    def __str__(self): # Overloading for __str__
        return '(%g + %gi)' % (self.real, self.imaginary)

complex1 = Complex(1,2)
complex2 = Complex(2,7)

print("{} + {} = {}".format(complex2, complex1, complex2+complex1))
print("{} - {} = {}".format(complex2, complex1, complex2-complex1))
~~~

  * 특정 객체간의 연산자를 재정의할 수 있다. (예 : ```+```, ```-``` 등 )
  * Overriding 의 내용인 것 같은데 "Operator Overloading" 이라고 부른다.
  * python 에서는 모든 연산자가 되는 것은 아니고 사전에 약속된 몇몇 인스턴스 연산자에 대해서 가능하다.
  * [python Operator Overloading](http://thepythonguru.com/python-operator-overloading/) 에서 오버로딩 가능한 연산자를 확인할 수 있다.

# Exception

~~~python
import traceback

a = 5
b = 0

try:
    if (type(a) is not int) or (type(b) is not int):
        raise TypeError("Unsupported type. Only Integer.")
    print(a/b)
except TypeError as ex:
	trace_msg = traceback.print_exc() # trace msg 를 string 으로 받는다.
    print(trace_msg)
except ZeroDivisionError as ex:
    print(ex) ## 해당 exception 의 __str__ 를 출력한다.
    traceback.print_exc() # console 에 trace msg 를 출력한다.
else:
    print("Success")
finally:
    print("Finally")
~~~

## Clean-up Action

~~~python
try:
    with open("fileName") as file:
        for line in file:
            print(line)
except IOError as ex:
    print(ex)
~~~

  * ``` with .... as variable ``` 구문은 with 구문을 벗어날 때 variable 을 무조건 close 시킨다.
  * ``` with .... as variable ``` 구문은 try 구문과 별개로 쓰일 수 있다.

## Customized Exception

~~~python
class UserException(Exception):
    __errors = {
        1: "Msg 1",
        2: "Msg 2",
        3: "Msg 3"
    }

    def __init__(self, code):
        self.code = code

    def __str__(self):
        return self.__errors[self.code]
~~~

# GUI & DB Access

~~~python
from tkinter import *
import sqlite3
import tkinter.messagebox


def isSQLite3(filename):
    from os.path import isfile, getsize

    if not isfile(filename):
        return False
    if getsize(filename) < 100: # SQLite database file header is 100 bytes
        return False

    with open(filename, 'rb') as fd:
        header = fd.read(100)

    return header[0:16] == b'SQLite format 3\000'

class SendInputsPage:
    def __init__(self, func, *args):
        self.func = func
        self.names = args[:-1]
        self.button_name = args[-1]
        self.frame_entry_tuple = []

    @staticmethod
    def __input_frame(root, label_name):
        frame = Frame(root)
        label_name = Label(frame, text=label_name)
        label_name.pack(side=LEFT)
        entry = Entry(frame, bd=5)
        entry.pack(side=RIGHT)
        return frame, entry

    def __message_frame(self, root, button_name):
        message_frame = Frame(root)
        button = Button(message_frame, text=button_name, command=self.__action)
        button.pack(side=LEFT)
        return message_frame

    def __action(self):
        inputs = []
        for frame in self.frame_entry_tuple:
          inputs.append(frame[1].get())
        self.func(inputs)

    def display(self):
        root = Tk()

        # input frame 생성
        for name in self.names:
            self.frame_entry_tuple.append(self.__input_frame(root, name))

        # message frame 생성
        message_frame = self.__message_frame(root, self.button_name)

        # frame 위치 배열
        message_frame.pack(side=BOTTOM)
        for frame in reversed(self.frame_entry_tuple):
            frame[0].pack(side=BOTTOM)

        # Page 출력 시작
        root.mainloop()


db_file_name = "customer.db"


def post_action(inputs):
    if isSQLite3(db_file_name):
        connection = sqlite3.connect(db_file_name)
        cursor = connection.cursor()
    else:
        connection = sqlite3.connect(db_file_name) # DB connection
        cursor = connection.cursor() # DB Cursor 생성

        # 최초 테이블 생성
        schema = "create table customer ( name VARCHAR(20) not null, address VARCHAR(60) null, email VARCHAR(40) null )"
        cursor.execute(schema) # sql 실행

    # 저장
    insert_sql = "insert into customer(name, address, email) values ('{}', '{}', '{}')".format(inputs[0], inputs[1], inputs[2])
    cursor.execute(insert_sql)
    connection.commit()

    # 저장완료 메시지팝업 출력
    tkinter.messagebox.showinfo("저장완료","이름 : {}, 주소 {} :, 이메일 : {}".format(inputs[0], inputs[1], inputs[2]))


page = SendInputsPage(post_action, "이름", "주소", "이메일", "저장")
page.display()

# 저장된 내역 전체 조회
connection = sqlite3.connect(db_file_name)
cursor = connection.cursor()
select_sql = "select name, address, email from customer"
cursor.execute(select_sql)
for name, address, email in cursor:
    print("이름 : {}, 주소 : {}, 이메일 : {}".format(name, address, email))
~~~
