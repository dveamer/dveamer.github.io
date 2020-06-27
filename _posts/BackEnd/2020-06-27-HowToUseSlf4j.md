---
layout: post
title: "SLF4J Logger 사용법 & 잘못된 사용법: Binding Parameters, Logging Exception Stack Trace"
date: 2020-06-27 00:00:00
lastmod: 2020-06-27 00:00:00
categories: BackEnd
tags: BackEnd SLF4J Log
---

![SLF4J Logo ](http://www.slf4j.org/images/logos/slf4j-logo.jpg){:class="imgTitle"} 
(이미지 출처 : [http://www.slf4j.org/](http://www.slf4j.org))  

Logger의 잘못된 사용법은 어플리케이션의 성능에 큰 영향을 줍니다.  
이러한 문제는 개발 과정 중에는 발견되지 않으며 성능 테스트에서야 발견이 가능합니다.  
성능 테스트 케이스에서 누락된 경우, 운영 중에 많은 부하를 받게 되었을 때 문제를 일으킬 소지가 있습니다.  

이 글은 SLF4J의 올바른 사용법에 대해서 기술합니다. 특히, 파라미터 바인딩과 exception의 stack trace를 로깅하는 올바른 방법에 대해서 기술합니다.  

<!--more-->

SLF4J(Simple Logging Facade for Java)를 이용해서 로그를 남기는 것을 가정하고 글을 작성합니다.  

[샘플](https://github.com/dveamer/SpringBootSample/blob/master/SLF4J_Log4J2/src/main/java/io/dveamer/sample/manual/ManualController.java)은 Github에 올려뒀습니다.  
샘플에서 SLF4J 와 연동한 로그 솔루션은 Log4J 2 입니다. 설정에 대한 설명은 아래 링크를 참고하시기 바랍니다.  

  * [SLF4J 와 Log4j 2 연동하기](/java/SLF4J-Log4J2.html)
  * [Log4j 2 설정하기](/java/Log4j2.html)

# 최악의 방법

```java.lang.System```을 이용한 로깅은 로그 레벨을 관리할 수 없기 때문에 문제가 됩니다.  

~~~java

    // worst
    System.out.println("Hello " + userName + ".");

~~~
 
Exception의 stack trace를 로깅할 때도 ```ex.printStackTrace()```를 이용하면 안됩니다.  
참고로, ```ex.printStackTrace()```은 내부적으로 ```java.lang.System```을 이용해서 로그를 남기게 됩니다.  

~~~java

    try {
        throw new Exception("Something is wrong.");
    } catch(Exception ex) {

        // worst
        ex.printStackTrace();
    }

~~~
 

```java.lang.System```이 아닌 로그 솔루션을 쓰면 로그 레벨 관리 뿐만이 아니라 로그를 파일 혹은 원격지에도 남길 수가 있고 비동기로 로깅을 할 수도 있는 등 다양한 이점이 있습니다.  


# Binding Parameters

## Binding One Parameter

아래 4개의 방법은 모두 동일한 결과를 출력합니다. 
그리고 INFO, WARN, ERROR 로그 레벨과 처럼 DEBUG보다 상위 레벨일 경우 4개 방법 모두 출력이 일어나지 않습니다.  
하지만 모두 출력이 일어나지 않음에도 불구하고 어떤 경우는 성능 차이가 납니다.  

~~~java

    private void bindOneParameter(String userName) {

        // poor performance, poor readability
        logger.debug("Hello " + userName + ".");

        // always good performance, poor readability
        if(logger.isDebugEnabled()) {
            logger.debug("Hello " + userName + ".");
        }

        // always good performance, good readability
        if(logger.isDebugEnabled()) {
            logger.debug("Hello {}.", userName);
        }

        // good performance, best readability - I recommend this.
        logger.debug("Hello {}.", userName);
    }


~~~

### 문자열 직접 연산 방법

~~~java
        // poor performance, poor readability
        logger.debug("Hello " + userName + ".");
~~~

```logger.debug()``` 메소드가 실행되기 전에 ```"Hello " + userName + "."``` 이라는 문자열 연산이 먼저 일어납니다.  

가용 로그 레벨을 DEBUG에서 INFO로 조절하면 로그가 남는 과정이 생략되기 때문에 성능 개선을 이뤄집니다.  
하지만 문자열 연산은 여전히 발생하기 때문에 문자열 연산만큼의 성능 악화가 발생됩니다.  

```+```와 같은 문자열 연산만의 문제가 아니라  
```logger.debug()``` 메소드 실행 이전에 어떠한 연산이라도 일어나게 되면 동일한 낭비가 발생됩니다.  

### 가용 로그 레벨 체크 

~~~java
        // always good performance, poor readability
        if(logger.isDebugEnabled()) {
            logger.debug("Hello " + userName + ".");
        }
~~~

가용 로그 레벨이 INFO라면 ```logger.debug()``` 메소드가 실행되지 않을 뿐만 아니라 문자열 연산도 일어나지 않기 때문에 성능 낭비가 없습니다.  


### 가용 로그 레벨 체크 & SLF4J 치환문자 사용

~~~java
        // always good performance, good readability
        if(logger.isDebugEnabled()) {
            logger.debug("Hello {}.", userName);
        }
~~~

두번째 방법과 동일하게 성능 낭비가 없습니다.  

게다가 SLF4J의 치환문자(```{}```)를 이용해서 가독성이 높아져 출력될 로그의 결과가 쉽게 예상됩니다.  


### SLF4J 치환문자 사용

~~~java
        // good performance, best readability - I recommend this.
        logger.debug("Hello {}.", userName);
~~~

먼저 첫번째 방법과 비교하면,  
```logger.debug()``` 메소드가 실행되기 전에 ```+```와 같은 연산은 없어서 성능 개선이 있습니다. 또한 로그의 가독성이 높아졌습니다.  

세번째 방법과 비교하면,  
```"Hello {}."``` 문자열 생성과 같은 연산이 발생하기 때문에 성능이 약간 떨어집니다.  

그리고 ```if```문이 없기 때문에 ```logger.debug()``` 메소드가 실행되는 것처럼 보이지만  
```logger.debug(String format, Object o)``` 내에서 로그 레벨을 체크하는 과정이 가장 먼저 일어나기 때문에 ```Object.toString()```과 같은 추가적인 연산은 발생하지 않습니다.  

다만, 로그 솔루션에 대한 필터를 등록한 경우에는 로그 레벨 체크 전에 필터가 먼저 수행됩니다.  
그렇기 때문에 필터를 작성시에 로그 레벨 체크를 고려해서 작성 해야한다는 주의사항이 발생하빈다.  

~~~java

package org.apache.logging.slf4j;

...(skipped)

public class Log4jLogger implements LocationAwareLogger, Serializable {

    private transient ExtendedLogger logger;

    ...(skipped)

    @Override
    public void debug(String format, Object o) {
        logger.logIfEnabled(FQCN, Level.DEBUG, (org.apache.logging.log4j.Marker)null, format, o);
    }

    ...(skipped)
}

~~~

[Log4J 2](http://logging.apache.org/log4j/2.x/)의 경우에는 아래와 같이 가용 로그를 가장 먼저 체크 합니다.  

~~~java

package org.apache.logging.log4j.spi;

...(skipped)

public abstract class AbstractLogger implements ExtendedLogger, Serializable {


    ...(skipped)

    @Override
    public void logIfEnabled(String fqcn, Level level, Marker marker, String message, Object p0) {
        if (isEnabled(level, marker, message, p0)) {
            logMessage(fqcn, level, marker, message, p0);
        }

    }

    ...(skipped)
}
~~~


다시 한번 살펴보면,  
네번째 방법은 세번째 방법보다 문자열 생성이라는 약간의 성능 낭비가 발생합니다.  
그리고 필터 작성시에 주의해야할 항목이 생깁니다.  
하지만 그럼에도 불구하고 코드의 간결성을 위해 저는 네번째 방법을 선호하고 권장합니다.  
그러한 이유로 앞으로 설명하는 내용들에서도 가용 로그 레벨을 먼저 체크하는 방법에 대해서는 생략하고 설명을 진행하겠습니다.  

만약 성능이 너무 중요한 어플리케이션이라면 가용 로그 레벨을 먼저 체크하는 방법을 사용하시기 바랍니다.  

## Binding Two Parameters

~~~java

    private void bindTwoParameters(User user) {

        // poor performance, poor readability
        logger.debug("Hello " + user.getName() + "(" + user.getEmail() + ")");

        // poor performance, good readability
        logger.debug(String.format("Hello %s(%s)", user.getName(), user.getEmail()));

        // good performance, good readability - I recommend this.
        logger.debug("Hello {}({}).", user.getName(), user.getEmail());
    }

~~~

파라미터가 1개 일때보다 2개의 경우, 가독성이 확실하게 비교됩니다.  
그리고 첫번째, 두번째 방법은 앞서 설명했던 것 처럼 문자열 연산이 가장 먼저 일어나기 때문에 성능 악화가 발생합니다.  

성능과 가독성을 모두 만족시켜주는 세번째 방법을 권장합니다.  

~~~java
        // good performance, good readability - I recommend this.
        logger.debug("Hello {}({}).", user.getName(), user.getEmail());
~~~


## Binding Many Parameters

~~~java
    private void bindManyParameters(User user) {

        // little poor performance
        logger.debug("User id : {}, name : {}, job : {}", user.getName(), user.getEmail(), user.getJob());

        // good performance - I recommend this.
        logger.debug("User : {}", user);
    }
~~~

두 방법 모두 SLF4J의 치환문자(```{}```)를 사용하는 방법입니다.  
다만, 첫번째 경우는 파라미터의 개수가 여러개이고 두번째 경우는 객체를 통째로 넘겨서 파라미터 개수를 1개로 줄였습니다.  

파라미터 개수가 3개 이상이면 가용 로그 레벨을 체크하기 전에 ```Object[]``` 를 생성하는 비용이 발생합니다.  

> However, this variant incurs the hidden(and relatively small) cost of creating an <code>Object[]</code> before invoking the method, even if this logger is disabled for DEBUG


~~~java

package org.slf4j;

...(skipped)

public interface Logger {

    ...(skipped)

    /**
     * Log a message at the DEBUG level according to the specified format
     * and arguments.
     * <p/>
     * <p>This form avoids superfluous string concatenation when the logger
     * is disabled for the DEBUG level. However, this variant incurs the hidden
     * (and relatively small) cost of creating an <code>Object[]</code> before invoking the method,
     * even if this logger is disabled for DEBUG. The variants taking
     * {@link #debug(String, Object) one} and {@link #debug(String, Object, Object) two}
     * arguments exist solely in order to avoid this hidden cost.</p>
     *
     * @param format    the format string
     * @param arguments a list of 3 or more arguments
     */
    public void debug(String format, Object... arguments);

    ...(skipped)
}
~~~

그렇기 때문에 최대한 파라미터의 개수를 2개 이하로 맞추기 위해 노력해야 합니다.  

당연한 이야기지만 User 객체의 ```Object.toString()``` 메소드에 대한 override를 통해 원하는 출력형태를 작성하셔야 합니다.  

~~~java
public class User {

    private final String email;
    private final String name;
    private final String job;

    ...(skipped)

    @Override
    public String toString() {
        return "User{" +
                "email='" + email + '\'' +
                ", name='" + name + '\'' +
                ", job='" + job + '\'' +
                '}';
    }
}
~~~

# Logging Exception

## Logging Exception Stack Trace


SLF4J는 [Throwable](https://cr.openjdk.java.net/~iris/se/11/latestSpec/api/java.base/java/lang/Throwable.html) 객체를 마지막 파라미터로 넘기게 되면 stack trace를 로깅시켜 줍니다. 다만, 첫번재 파라미터는 String으로 넘겨주셔야 합니다.  

~~~java
    private void printExceptionStackTrace(User user) {

        try {
            throw new Exception("Something is wrong.");
        } catch(Exception ex) {

            // good performance, poor information
            logger.error("", ex);

            // good performance, good information - I recommend this.
            logger.error("User : {}", user, ex);
        }
    }
~~~

위의 모두 아래와 같이 동일한 stack trace가 로깅 됩니다.  

~~~log
java.lang.Exception: Something is wrong.
	at io.dveamer.sample.manual.ManualController.printExceptionStackTrace(ManualController.java:87) [main/:?]
	at io.dveamer.sample.manual.ManualController.manual(ManualController.java:23) [main/:?]
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:?]
	at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:?]
	at java.lang.reflect.Method.invoke(Method.java:566) ~[?:?]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190) [spring-web-5.2.6.RELEASE.jar:5.2.6.RELEASE]
  ...(skipped)
~~~

하지만 첫번째 방법은 stack trace만 남기 때문에 문제 해결에 필요한 정보가 부족합니다.  
두번째 방법은 아래와 같이 User 객체의 정보가 함께 로깅 되기 때문에 보다 빠른 문제 해결이 가능합니다.  

~~~log
2020-06-27 23:54:41,974 ERROR i.d.s.m.ManualController [http-nio-8080-exec-1] User : User{email='dveamer@gmail.com', name='Dveamer', job='developer'}
java.lang.Exception: Something is wrong.
	at io.dveamer.sample.manual.ManualController.printExceptionStackTrace(ManualController.java:87) [main/:?]
	at io.dveamer.sample.manual.ManualController.manual(ManualController.java:23) [main/:?]
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method) ~[?:?]
	at jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62) ~[?:?]
	at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43) ~[?:?]
	at java.lang.reflect.Method.invoke(Method.java:566) ~[?:?]
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:190) [spring-web-5.2.6.RELEASE.jar:5.2.6.RELEASE]
~~~

[Binding Many Parameters](#Binding Many Parameters) 때와 마찬가지로 파라미터는 exception을 포함해서 2개 이하로 맞추기 위해 노력해야합니다.  

## Logging Exception Message

~~~java

    private void printExceptionMessage(User user) {

        try {
            if(user.getName() == null) {
                throw new IllegalArgumentException("user name is required parameter.");
            }
        } catch(IllegalArgumentException ex) {

            // good performance, poor information
            logger.warn(ex.getMessage());

            // poor performance, good information
            logger.warn("{} - User : {}", ex.getMessage(), user.toString());

            // good performance, good information - I recommend this.
            logger.warn("{} - User : {}", ex.getMessage(), user);
        }
    }

~~~

첫번째 방법은 예외 처리 메시지는 잘 기록 되지만 상황에 대한 정보가 부족합니다.  
```ex.getMessage()```의 경우에도 가용 로그 레벨 체크보다 먼저 수행되지만  
일반적인 exception들의 ```getMessage()``` 메소드는 getter의 역할만 하기 때문에 비용 발생이 거의 없습니다.  

두번째 방법은 필요한 정보가 모두 기록되지만 가용 로그 레벨 체크보다 ```user.toString()```이 먼저 수행되기 때문에 성능 낭비가 발생합니다.  

세번재 방법은 가용 로그 레벨 체크가 ```user.toStirng()```보다 먼저 수행되기 때문에 성능 낭비가 발생하지 않습니다.  
이 경우에도 역시 [Binding Many Parameters](#Binding Many Parameters) 때와 마찬가지로 파라미터는 exception을 포함해서 2개 이하로 맞추기 위해 노력해야합니다.  



