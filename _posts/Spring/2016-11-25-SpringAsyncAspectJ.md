---
layout: post
title:  "Spring @Async AspectJ 비동기처리"
date:   2016-11-25 12:00:00 
lastmod: 2019-01-05 12:00:00 
categories: Java
tags: Spring Asynchronous Weaving AspectJ AOP
---

![https://spring.io/](https://spring.io/images/spring-logo-fc4350c59999bb62c468361537212419.svg){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 [Spring @Async 비동기처리](/java/SpringAsync.html) 에서 알려드린 Spring @Async의 제약사항에 대해 다시 한번 살펴보고  
CTW(Compile-Time Weaving)과 LTW(Load-Time Weaving)라는 두가지 방식으로 AspectJ를 이용해서 제약사항을 회피하는 방법을 공유드립니다.  
  
<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

[CTW 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncCTW)과 [LTW 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncLTW)을 Github에 올려뒀으니 참고하시기 바랍니다.  


# @Async 와 AOP

Spring @Async는 Spring AOP(Aspect-Oriented Programming)를 활용해서 제공되는 기능입니다.  
그러한 이유로 Spring AOP의 특성이나 제약사항을 Spring @Async가 그대로 가지게 됩니다.  

Spring AOP는 Proxy 모드와 AspectJ 모드가 존재합니다.  

# Spring AOP Proxy 모드

PROXY 모드는 Spring AOP의 기본 모드이며, 순수한 Java 코드로 AOP를 구현합니다.  
비동기처리와 같은 관점(Aspect)를 특정 method와 엮는작업(Weaving)이 Java 코드가 실행되는 시점에 이뤄집니다.  
RTW(Run-Time Weaving)으로 처리된다고 표현하고  
특정 method가 호출되는 런타임 시기에 해당 method의 비동기처리 여부가 정해집니다.  

AOP는 비동기처리 뿐만 아니라 다양한 목적으로 활용됩니다.  

## 제약사항
  * pulbic method에만 사용가능 합니다.  
  * 같은 객체내의 method끼리 호출시 AOP가 동작하지 않습니다.  
  * RTW로 처리 되기 때문에 약간의 성능저하가 있습니다.  

이 제약사항은 @Async, @Transactional과 같은 Spring AOP를 활용하는 다른 기능들에게도 동일하게 적용됩니다.  

# Spring AOP AspectJ 모드 

## AspectJ

AspectJ는 Spring과 별개의 프로그램입니다.  
Java로 작성된 코드라면 Spring framework을 쓰지 않아도 AspetJ 만으로도 AOP를 구성할 수 있습니다.  
예를들어 Android application에도 AOP를 구성할 수 있습니다.  

AspectJ는 두번의 컴파일 과정이 필요합니다.  
1차적으로 Java 코드를 컴파일해서 binary 형태의 class 파일을 만들고  
2차적으로 binary 파일들을 분석해서 필요한 부분에다가 aspect를 삽입하는 weaving 과정을 거칩니다.  

CTW(Compile-Time Weaving)으로 처리된다고 표현합니다.  

## Spring AOP AspectJ 모드

Spring AOP에 AspectJ를 활용할 수 있습니다.  
그 덕분에 @Async annotation과 같이 굉장히 간결한 방식으로 AspectJ의 유연성을 활용할 수 있습니다.  

Spring에서 AspectJ를 활용하는 방법은  
CTW(Compile-Time Weaving)과 LTW(Load-Time Weaving)로 두가지가 있습니다.  

두 방법 모두 Proxy 모드가 갖는 제약사항을 처리할 수 있고  
LTW의 경우는 class를 load하는 과정에서만 weaving을 하기 때문에 RTW보다는 성능이 빠릅니다.  
CTW는 컴파일시에만 weaving을 하기 때문에 성능이 가장 좋습니다.  
대신 Proxy 모드에서 추가적인 설정작업이 필요합니다.  

Proxy 모드만으로도 개발하는데 큰 문제는 없습니다.  
만약에 AspectJ 모드가 필요하다면 CTW, LTW 중에 한가지 방식을 골라서 설정하시면 됩니다.  

이 설정도 Spring이 추구하는 방식대로 소스코드는 수정하지 않습니다.  
build.gradle과 configuration 수정만으로 진행됩니다.  
갑자기 프로젝트에 CTW 방식으로 @Async를 바꿔야한다고해서 소스코드를 수정되지 않기 때문에  

개발자가 개발단계에서 비동기처리를 CTW로할지 RTW로 할지 고민할 필요가 없다는 얘기입니다.  

# Spring @Async CTW 설정

[Spring Async 샘플](https://github.com/dveamer/SpringBootSample/tree/master/Async)에서부터 수정해나가는 방식으로 설명하겠습니다.  
최종적으로 수정된 [CTW 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncCTW)도 참고하시기 바랍니다.  

## build.gradle 파일 수정

~~~gradle

buildscript {
    ext {
        ...
        eveohAspectj = '1.6'
        aspectjVersion = '1.8.4'
    }

    repositories {
        maven {
            url("https://maven.eveoh.nl/content/repositories/releases")
        }
        ...
    }
    dependencies {
        ...
        classpath("nl.eveoh:gradle-aspectj:${eveohAspectj}")
    }
}

...
apply plugin: "aspectj"
apply plugin: 'application'

...

dependencies {

    ...

    // AspectJ - Compile Time Weaver
    compile("org.springframework:spring-aspects")
    aspectpath("org.springframework:spring-aspects")
}
~~~

  * ```apply plugin: "aspectj"```를 추가할 때 aspectjVersion 변수보다 아래 라인에서 추가해야만 합니다.  

## SpringAsyncConfig.java 설정 수정

~~~java
@Configuration
@EnableAsync(mode=AdviceMode.ASPECTJ)
public class SpringAsyncConfig {
    ...
}
~~~

@Async를 AspectJ를 통해서 설정하겠다는 의미로  
@EnableAsync를 설정할 때 mode를 AdviceMode.ASPECTJ 로 추가 설정했습니다.  

## 테스트 

테스트 내용은 아래와 같습니다.  
Controller로부터 service의 method1과 method2가 호출됩니다.  
그리고 method2는 method3을 내부호출합니다.  
그리고 method3는 private 으로 클래스내의 내부호출만 가능합니다.  

### Proxy 모드 결과

~~~log
2016-11-26 18:10:54,216 INFO i.d.s.h.GreetingController [http-nio-8080-exec-1] Hello World
2016-11-26 18:10:54,271 INFO i.d.s.h.GreetingService [Executor-1] method 1-1
2016-11-26 18:10:54,274 INFO i.d.s.h.GreetingService [Executor-2] method 2-1
2016-11-26 18:10:54,275 INFO i.d.s.h.GreetingService [Executor-2] method 3-1
2016-11-26 18:10:54,375 INFO i.d.s.h.GreetingService [Executor-2] method 3-2
2016-11-26 18:10:54,375 INFO i.d.s.h.GreetingService [Executor-2] method 2-2
2016-11-26 18:10:54,571 INFO i.d.s.h.GreetingService [Executor-1] method 1-2
~~~

GreetingController에서 Hello Wordl를 추력한 thread의 이름은 http-nio-8080-exec-1 입니다.  
GreetingService의 method1을 실행한 thread는 Executor-1이고  
Executor-2가 method2, method3을 실행했습니다.  

Proxy 모드의 경우 내부호출되는 경우에는 비동기처리가 불가능하기 때문에  
method3는 다른 thread로 분리되어 처리되지 않고 Executor-2가 함께 처리했습니다.  

### CTW AspectJ 모드 결과

~~~log
2016-11-26 04:30:25,574 INFO i.d.s.h.GreetingController [http-nio-8080-exec-1] Hello World
2016-11-26 04:30:25,580 INFO i.d.s.h.GreetingService [Executor-1] method 1-1
2016-11-26 04:30:25,581 INFO i.d.s.h.GreetingService [Executor-2] method 2-1
2016-11-26 04:30:25,582 INFO i.d.s.h.GreetingService [Executor-2] method 2-2
2016-11-26 04:30:25,582 INFO i.d.s.h.GreetingService [Executor-3] method 3-1
2016-11-26 04:30:25,683 INFO i.d.s.h.GreetingService [Executor-3] method 3-2
2016-11-26 04:30:25,880 INFO i.d.s.h.GreetingService [Executor-1] method 1-2
~~~

GreeingService의 method별로 tread명이 각각 다른 것을 확인할 수 있습니다.  
내부호출된 method3도 새로운 thread로 실행되는 것을 확인할 수 있습니다.  


# Spring @Async LTW 설정

작성중입니다.


