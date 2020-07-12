---
layout: post
title: "유용한 표준 Java RuntimeException"
date: 2020-07-12 00:00:00
lastmod: 2020-07-12 00:00:00
categories: BackEnd
tags: BackEnd Java
---

![https://openjdk.java.net](https://openjdk.java.net/images/openjdk.png){:class="imgTitle"}  
( 이미지 출처 : [https://openjdk.java.net](https://openjdk.java.net) )  

Java 프로그래밍을 하면서 예외처리를 발생시켜야하는 경우, 우리는 RuntimeException을 상속하는 예외를 사용하면 됩니다.  
그리고 RuntimeException을 상속하는 예외를 새롭게 만드는 것보다는 JDK에서 제공하는 표준 RuntimeExcepton 상속 예외들을 사용하는 것이 바람직합니다.  

JDK 12 기준으로 RuntimeException을 직접 상속하는 예외는 총 58개입니다. (참고 - [OpenJdk 12 RuntimeException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/RuntimeException.html))  
그리고 그 58개의 예외들을 다시 상속하는 자식 예외들까지 개수를 세면 엄청나게 많습니다.  


그 중 자주 사용하게 되는 유용한 표준 RuntimeException 들을 기록합니다.  

<!--more-->

참고로 전체 계층 구조를 보고 싶으시다면 Intellij의 Hierarchy 기능을 이용하시면 쉽게 확인 가능합니다.  
예를들어 RuntimeException의 전체 계층 구조를 보고 싶다면 아래와 따라하시면 됩니다.  

  * ```shift``` 연속 두번 누른 후 ```RuntimeException``` 입력 후 선택 : RuntimeException class가 열림  
  * ```Ctrl``` + ```H```  

## IllegalArgumentException

[IllegalArgumentException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/IllegalArgumentException.html) 은 잘못된 인수를 가진 호출을 받았을 때 사용하면 됩니다.  

IllegalArgumentException를 다시 상속하는 예외들도 많이 있습니다. 아래 예시는 그 중 몇가지 자주 사용되는 예외들입니다.  
다양한 부적절한 인슈 유형들에 대해서 좀 더 구체적인 예외를 발생시킬 수 있게 해줍니다.  

  * [IllegalFormatException ](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/IllegalFormatException.html) : available in 1.5 or later
  * InvalidKeyException : available in 1.5 or later
  * InvalidParameterException : available in 1.1 or later
  * InvalidPathException : available in 1.7 or later
  * KeyAlreadyExistsException : available in 1.5 or later
  * NumberFormatException : available in 1.0 or later
  * ProviderMismatchException : available in 1.7 or later


## IllegalStateException

[IllegalStateException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/IllegalStateException.html) 은 호출받은 객체가 요청을 수행하기에 적합하지 않은 상태일때 사용하면 됩니다.  

  * AcceptPendingException : available in 1.7 or later
  * AlreadyBoundException : available in 1.7 or later
  * CancellationException : available in 1.5 or later
  * CancelledKeyException : available in 1.4 or later
  * ReadPendingException : available in 1.7 or later
  * WritePendingException : available in 1.7 or later


## NullPointerException

[NullPointerException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/NullPointerException.html) 은 null을 허용하지 않는 메소드에 null이 인자로 넘어왔을 때 사용하면 됩니다.  

이 또한 잘못된 인자에 대한 에러이고 IllegalArgumentException을 사용해야하는 것이 아닌가? 라는 의문이 들수 있습니다. 관례적으로, null에 대해서는 IllegalArgumentException이 아니라 NullPointerException을 사용합니다.  


## IndexOutOfBoundsException

[IndexOutOfBoundsException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/IndexOutOfBoundsException.html) 은 인덱스가 허용범위를 넘어갔을 경우 사용하면 됩니다.  

이 또한 잘못된 인자에 대한 에러이고 IllegalArgumentException을 사용해야하는 것이 아닌가? 라는 의문이 들수 있습니다. 관례적으로, 인덱스가 허용범위를 넘어간 경우에 대해서는 IllegalArgumentException이 아니라 IndexOutOfBoundsException를 사용합니다.  

## ConcurrentModificationException

[ConcurrentModificationException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/ConcurrentModificationException.html) 은 허용되지 않는 동시 수정이 발생되었을 때 사용하면 됩니다.  

## UnsupportedOperationException

[UnsupportedOperationException](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/lang/UnsupportedOperationException.html) 은 지원되지 않는 메소드가 호출되었을 때 사용하면 됩니다.  

