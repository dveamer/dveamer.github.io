---
layout: post
title: "Java Validation - null 체크, Optional"
date: 2020-04-04 00:00:00
categories: BackEnd
tags: BackEnd Java
---

생성자 혹은 public/protected method는 입력 파라미터에 대해서 제약사항을 지키는지 확인해야한다.  

> 오류는 가능한 한 빨리 (발생한 곳에서) 잡아야 한다.  
> 매개변수 검사를 제대로 하지 못하면 몇가지 문제가 발생할 수 있다.  
> 첫 번째, 메서드가 수행되는 중간에 모호한 예외를 던지며 실패할 수 있다.  
> 더 나쁜 상황은 메서드는 문젱벗이 수행됐지만, 어떤 객체를 이상한 상태로 만들어 놓아서 미래의 알수 없는 시점에 이 메서드와 관련없는 오류를 낼 때다.  
> - 참조 : Effective Java 3/E 한글판 298 page

이번 글에서는 가장 대표적인 검사대상인 null에 대한 체크 방법을 알아볼 예정이다.  

<!--more-->

아래는 생성자에서 if문을 이용한 null 체크를 하는 내용이다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        if(userName==null) {
            throw new IllegalArgumentException("The userName cannot be null"); 
        }
        this.userName = userName;
    }
} 
~~~

이와 유사한 작업을 좀 더 나은 방법으로 어떻게 할 수 있는지 알아보자.  

# java.util.Obejcts

[java.util.Obejcts](https://docs.oracle.com/javase/10/docs/api/java/util/Objects.html)은 JDK 7부터 Java 언어자체에서 제공하는 기능이기 때문에 다른 라이브러리 참조도 필요 없다.  

아래 5개 method는 Objects가 가진 null 체크 관련된 method들이다.  

> static \<T\> T requireNonNull​(T obj) : Checks that the specified object reference is not null.  
> static \<T\> T requireNonNull​(T obj, String message) : Checks that the specified object reference is not null and throws a customized NullPointerException if it is.  
> static \<T\> T requireNonNull​(T obj, Supplier\<String\> messageSupplier) : Checks that the specified object reference is not null and throws a customized NullPointerException if it is.  
> static \<T\> T requireNonNullElse​(T obj, T defaultObj) : Returns the first argument if it is non-null and otherwise returns the non-null second argument.  
> static \<T\> T requireNonNullElseGet​(T obj, Supplier\<? extends T\> supplier) : Returns the first argument if it is non-null and otherwise returns the non-null value of supplier.get().  
> - 참조 : [java.util.Obejcts](https://docs.oracle.com/javase/10/docs/api/java/util/Objects.html)  


## requireNonNull​

첫번째 ```requireNonNull​```는 input 값이 not-null이면 input을 리턴하고 null 이라면 NullPointException을 발생시켜준다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 7 이상에서 사용 가능
        this.userName = Objects.requireNonNull(userName);
    }
}
~~~


두번째 ```requireNonNull​```는 NullPointException의 메시지를 정할 수 있게 해준다.  
세번째 ```requireNonNull​```는 두번째와 비슷한데 메시지를 생성하는 단계를 lazy하게 처리할 수 있게 해준다. 만약에 메시지 생성에 연산이 들어간다면 not-null인 경우에 성능적 이점이 있다.  
하지만 개인적으로 null 체크에서 메시지 생성에 연산이 필요한 경우가 있는지는 모르겠다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 7 이상에서 사용 가능
        this.userName = Objects.requireNonNull(userName, "The userName cannot be null");

        // JDK 8 이상에서 사용 가능
        this.userName = Objects.requireNonNull(userName, () -> "The userName cannot be null");
    }
} 
~~~

## requireNonNullElse​

```requireNonNullElse​```는 input 값이 not-null이면 input을 리턴하고 null 이라면 default 값을 리턴한다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 10 이상에서 사용 가능
        this.userName = Objects.requireNonNullElse(userName, "홍길동");
    }
} 
~~~


## requireNonNullElseGet

```requireNonNullElseGet```는 input 값이 not-null이면 input을 리턴하고 null이면 supplier.get()의 결과를 default 값으로 리턴한다.  
client로부터 supplier를 제공받는다면 default 값 세팅이 유연해진다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 10 이상에서 사용 가능
        this.userName = Objects.requireNonNullElseGet(userName, () -> "홍길동");
    }
} 
~~~


# java.util.Optional<T>

[Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)은 JDK 8부터 Java 언어자체에서 제공하는 기능이기 때문에 다른 라이브러리 참조도 필요 없다.  

## orElse

```orElse```은 input 값이 not-null이면 input을 리턴하고 null이면 default값을 리턴한다.  
여기서는 userName이 not-null이면 userName을 리턴하고 아니면 "홍길동"을 리턴한다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 8 이상에서 사용 가능
        this.userName = Optional.ofNullable(userName).orElse("홍길동");
    }
} 
~~~

## orElseThrow

```orElseThrow```은 input이 null일 때 NoSuchElementException을 발생시켜 준다. 다른 Exception을 발생시키고 싶다면 Supplier를 파라미터로 넘겨주면 된다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        // JDK 10 이상에서 사용 가능
        this.userName = Optional.ofNullable(userName)
                                .orElseThrow();

        // JDK 8 이상에서 사용 가능
        this.userName = Optional.ofNullable(userName)
                                .orElseThrow(()->new IllegalArgumentException("The userName cannot be null"));
    }
} 
~~~

# org.springframework.util.StringUtils

Spring에서 제공하는 [StringUtils](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/StringUtils.html)는 string에 관련된 기능들을 제공해준다. 그 중 isEmpty는 null 체크시 유용하다.  


## isEmpty

그 중 isEmpty는 input이 null 혹은 빈값("")일 경우 true를 리턴해준다.  
null 체크만이 아닌 빈값에 대한 체크가 필요할 때 유용하다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        if(StringUtils.isEmpty(userName)) {
            throw new IllegalArgumentException("The userName cannot be null / empty"); 
        }
        this.userName = userName;
    }
} 
~~~


# org.apache.commons.lang3.StringUtils

[commons.apache.org](http://commons.apache.org)에서 제공하는 [StringUtils](http://commons.apache.org/proper/commons-lang/apidocs/org/apache/commons/lang3/StringUtils.html)은 string에 관련된 기능들을 제공해준다. 그리고 그 기능들은 대부분 null-safe하게 사용할 수 있어서 유용하다.  

## isEmpty

그 중 isEmpty는 input이 null 혹은 빈값("")일 경우 true를 리턴해준다.  
null 체크만이 아닌 빈값에 대한 체크가 필요할 때 유용하다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        if(StringUtils.isEmpty(userName)) {
            throw new IllegalArgumentException("The userName cannot be null / empty"); 
        }
        this.userName = userName;
    }
} 
~~~


## isBlink

null 혹은 빈값("") 그리고 whitespace(" ")인 경우 true를 리턴해준다.  

~~~java
public class User {

    private final String userName;

    public User(String userName) {
        if(StringUtils.isBlink(userName)) {
            throw new IllegalArgumentException("The userName cannot be null / empty / whitespace"); 
        }
        this.userName = userName;
    }
} 
~~~

## null-safe

제공되는 다양한 기능들에 모두 null 체크와 기본값 처리가 들어가있어서 null safe하다.  
예를들어, Java에서 제공하는 startsWith는 체크대상이 null일 경우 NullPointException이 발생하지만 StringUtils.startsWith는 null 체크까지 해주기 때문에 NullPointException가 발생하지 않는다.  


~~~java
public class User {

    private final String userName;

    public User(String userName) {
        if(userName!=null && userName.startsWith("1") {
            throw new IllegalArgumentException("The username cannot be started with 1"); 
        }
        if(StringUtils.startsWith(userName, "1") {
            throw new IllegalArgumentException("The username cannot be started with 1"); 
        }
        this.userName = Objects.requireNonNullElse(userName, "홍길동");
    }
} 
~~~


# assert

항상 참이어야 하는 조건들을 assert와 함께 사용해서 파라미터를 검증할 수도 있다. 참이 어야하는 조건들이 만족되지 않으면 AssertionError가 발생한다.  

java 실행명령에 ```-ea``` 혹은 ```--enableassertions``` 옵션을 주지 않으면 런타임시에 실행되지 않을 뿐 아니라 성능저하가 발생하지 않는다.  
대신 실행되지 않기 때문에 사용자가 던지는 파라미터를 validation하진 못한다. 사용자가 던지는 파라미터는 생성자, public/protected method 등에서 검증하고 assert는 개발, 테스트 단계에서  private 메소드들의 input 파라미터에 대한 검증을 위해 사용한다.  

~~~java
    private void method(String input) {

        assert input != null;
        assert !input.startsWith("1");

        // do something.
    }
~~~
