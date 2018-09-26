---
layout: post
title:  "인터뷰 준비"
date:   2016-10-17 12:00:00 
lastmod: 2018-09-26 00:00:00 
categories: Summary
tags: Summary
---

인터뷰 준비

지금까지 쌓아온 것들을 가볍게 정리하며 사이사이의 구멍들을 메꾸는 시간..

<!--more-->

참고 : [다른 사람의 Summary](http://hahahoho5915.tistory.com/16)

# 개념

## OS
  * OSI 7 Layers  
    ![OSI 7 Layers](http://cfile23.uf.tistory.com/image/2153243B542D2CC6187741)  
    - [계층별 설명](http://beansberries.tistory.com/entry/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-OSI-7-%EA%B3%84%EC%B8%B5  )
  * 사용해본 OS 종류 : Redhat 5.5, Solaris 10, Ubuntu 14.04LTS, Windows Server
  * 명령어 : Process Status, free, top, df, kill, killall, wc, awk, find, grep
  * Bash, Crontab 
  
## Java
  * JAVA, JVM 의 특성  
    * 자바가상머신(JVM)만 설치하면 컴퓨터의 운영체제에 상관없이 작동한다.(즉, 운영체제에 독립적)  
    * 기본 자료형을 제외한 모든 요소들이 객체로 표현  
    * 객체 지향 개념의 특징인 캡슐화, 상속, 다형성이 잘 적용된 언어  
    * Garbage Collector를 통한 자동적인 메모리 관리  
    * 멀티쓰레드(Multi-thread)를 지원  
    * JVM은 Java만이 아닌 다양한 언어에게 위와 같은 장점을 제공한다  
      - Clojure, Apache Groovy, Scala, Kotlin, JRuby, Jython 등  

  * Generic : JDK 1.5부터 제공
  * Annotation : JDK 1.5부터 제공
    - public <A extends Annotation> A getAnnotation(Class<A> annotationClass)
    - public Annotation[][] getParameterAnnotations()
    - @Retention(RetentionPolicy.RUNTIME)
    - @Target
    - 주로 Reflection 에 의해 활용됨
  * Stream, Functional Programming
  * Process 와 Thread 차이


  * equals & hashcode
    - 수정시에는 둘다 같이 수정해줘야함
    - Java에서 기본적으로 구현해 둔 Collection, Map 들은 equals과 hashcode를 함께 이용해서 동일여부를 판단함

  * equals (동질성-래퍼런스의 값이 같다) & == (동일성-래퍼런스의 주소가 같다)


## Java Memory

### 구조
  ![Java 메모리 구조](http://www.gliderwiki.org/resource/real/57/20130531/thumb/thumb_2013053121572644971610970.jpg)
 
  * [Java 메모리 구조 1](http://www.gliderwiki.org/wiki/76)
  * [Java 메모리 구조 2](http://m.blog.daum.net/rightvoice/99)

### Memory Error 종류
  * java.lang.StackOverflowError
    - Stack size의 부족으로 지역변수, 메소드, 부분결과 등을 할당하지 못할 경우 발생
  * java.lang.OutOfMemoryError: Java heap spac 
    - Heap size의 부족으로 Java Object를 Heap에 할당하지 못하는 경우 발생
  * java.lang.OutOfMemoryError: PermGen space
    - Class나 Method 객체를 PermGen space에 할당하지 못하는 경우 발생
    - 애플리케이션에서 너무 많은 class를 로드할 때 발생
  * java.lang.OutOfMemoryError: (Native method)
    - JVM에 설정된 것 보다 큰 native메모리가 호출 될 경우 발생

#### References
  * [Out Of Memory 오류](http://www.nextree.co.kr/p3878/)

### Garbage Collection & Memory Leak 분석
  * [VisualVM & VisualGC](http://www.oracle.com/technetwork/java/visualgc-136680.html)
  * [PMAT(IBM Pattern Modeling & Analysis Tool)](https://www.ibm.com/developerworks/community/groups/service/html/communityview?communityUuid=22d56091-3a7b-4497-b36e-634b51838e11)
  * jstat
  * Heep dump : jmap

### GC Algorithm 종류
  * Serial GC
  * Parallel GC
  * Parallel Old GC
  * CMS GC
    - Marking시에만 Stop-The-World가 발생하고 시간이 짧음
    - Concurrent GC는 STW 없이 진행하지만 Old 영역에 대한 Compaction 작업을 수행하지 않기 때문에 단편화가 심함
    - 전체적으로는 메모리가 여유롭지만 단편화 때문에 새로운 객체를 적재하지 못하는 Promotion Failure 발생 가능 -> Full GC 발생 (Log : concurrent mode failure)
    - Old 영역이 실 사용량에 비해 너무 타이트하면 단편화 때문에 Full GC가 반복발생 가능
    - Full GC 발생시 Compaction도 일어나지만 다른 GC에 비해 비용이 큼
  * G1 GC

#### References
  * [Java Garbage Collection](http://d2.naver.com/helloworld/1329)
  * [Akka.IO 및 Netty의 out of memory 문제를 해결하기까지.](https://purluno.wordpress.com/2015/04/16/akka-io-%EB%B0%8F-netty%EC%9D%98-out-of-memory-%EB%AC%B8%EC%A0%9C%EB%A5%BC-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0%EA%B9%8C%EC%A7%80/)
  * [오랜만에 Garbage Collection 정리](http://yckwon2nd.blogspot.kr/2014/04/garbage-collection.html)

## Design Pattern

  * 팩토리 패턴
    - 객체 생성을 캡슐화 하는 패턴
    - 객체 생성이라는 바뀔 수 있는 내용을 기존 코드에서 분리해내는 것이 목적

  * 변형된 팩토리 패턴
    - 추상 팩토리 패턴 : 팩토리 패턴에 전략패턴을 적용
    - 팩토리 메소드 패턴 : 팩토리 패턴에 템플릿 메소드 패턴을 적용
    - 전략의 종류가 다양해짐으로 인해 팩토리 소스가 변하는 것을 방지하는 것이 목적
    - 새로운 객체가 추가되더라도 기존 소스의 수정이 필요없다
    - CoupangFactory, LGCNSFactory : createBackEndDeveloper, createFrontEndDeveloper, createDesigner 
    - http://seotory.tistory.com/27

  * 어뎁터 패턴
    - 기존객체가 가지고 있는 인터페이스와 다른 인터페이스를 고객에게 제공하는 것이 목적

  * 데코레이터 패턴
    - 동적으로 객체에게 역확이나 기능을 추하가는 것인데 반해 proxy패턴은 proxy 객체를 이용해 기존 객체에 대한 접근제어나 성능 향상등 부수적인 작업을 수행하는것이 목적

  * 프록시 패턴
    - 기존 객체가 가지고 있는 기능에 동일한 인터페이스를 제공하되 부가적인 기능이나 역활을 수행하는것이 목적

  * 싱글톤
    - 인스턴스가 하나 뿐인 특별한 객체를 만들 수 있게 해주는 패턴

  * 커맨드 패턴
    - execute method
    - 하나의 객체가 하나의 커맨드가 되며, 객체를 선택해서 실행하면 원하는 목적을 얻을 수 있다.

  * 템플릿 메소드 패턴
    - 바뀌지 않는 부분은 템플릿화하고 바뀌는 부분은 abstract 메소드로 구성
    - 서브 클래스에서 바뀌는 내용을 기술, 바뀌지 않는 부분은 상속받음
    - Client는 abstact 클래스의 메소드를 호출

  * 전략패턴 
    - 인터페이스의 구체 클래스를 전략적으로 선택할 수 있는 패턴
    - 바뀌는 부분에 대해서 전략적으로 선택하도록 구성
    - 기존 코드의 수정없이 전략을 추가하는 것이 가능하다
    - 템플릿 메소드 패턴보다 더 유연하다
    - Client 는 context의 메소드를 호출한다. context 는 전략패턴의 interface를 사용하는 클래스를 의미함.

  * 템플릿 콜백 패턴
    - 전략패턴 기본 구조에 익명 내부 클래스(callback)를 활용한 방식
    - context 를 실행시킴과 동시에 client가 callback을 context에게 DI 시킴

#### References
  * [Design Pattern](http://mret.byus.net/moniwiki/wiki.php/DesignPattern)

## Data Structure
  * Collection은 Array보다 유연성 측면에서 유리하다.

  * List, Set, Map 차이점
    - List, Set 은 Collection 을 상속
    - List 는 순차적으로 수집, 사용을 목적
    - Set 은 중복 불가
    - Map 은 key, value 형태로 입력. 순서기억, 중복 불가. search에 유리함

  * List Sort  

~~~java
List<Contact> contacts = new ArrayList<Contact>();
// Fill it.

// Now sort by address instead of name (default).
Collections.sort(contacts, new Comparator<Contact>() {
    public int compare(Contact one, Contact other) {
        return one.getAddress().compareTo(other.getAddress());
    }
}); 
~~~

  * Itrable : interface
    * Collection : interface
      - List : interface
        - LinkedList : class
        - ArrayList : class
        - Stack : class : push, pop, peek
      - Set : interface
        - HashSet : class
        - SortedSet : interface
          - TreeSet : class
      - Queue : interface : enQueue, deQueue
  * Map : interface
    * Hashtable : class
    * HashMap : class
    * SortedMap : interface
        - [TreeMap](http://egloos.zum.com/iilii/v/4537561) : class : red-black tree (self-balancing binary search tree), sorted KeySet 제공

  * [Java 1.6 collections UML class diagrams](http://www.jroller.com/VelkaVrana/entry/java_1_6_collections_class)

## Network
  * OSI 7 Layers  
    ![OSI 7 Layers](http://cfs4.tistory.com/upload_control/download.blog?fhandle=YmxvZzkyNjM0QGZzNC50aXN0b3J5LmNvbTovYXR0YWNoLzAvMC5naWY%3D)
  * TCP protocol의 연결 및 종료 과정  
   ![TCP](https://farm1.staticflickr.com/440/18338404268_f693b065d4_o.png)  
   ( 출처 : [CLOSE_WAIT 문제해결](http://docs.likejazz.com/close-wait/) )  
  * TCP 와 UDP 비교
    * TCP는 데이터의 신뢰성을 보장한다. 데이터가 유실될 경우 재전송을 하며 순서도 보장한다.
    * UDP는 데어터의 신뢰성을 보장하지 못 하지만 TCP 보다 전송속도가 빠르다.
  * 라우터(L3)와 스위치(L2)의 차이
    * L2 는 Mac address, L3는 IP address 로 전송 목적지를 구분한다.
    * L2스위치는 는 L2 와 직접 연결된 Device 에게만 전송이 가능하다. (2촌까지만 전달가능)
    * 라우터는 IP 라우팅 테이블의 정보를 이용해서 목적지인 device 혹은 다른 라우터로 전송이 가능하다. (2촌 이상 전달가능)
  * L4와 L7
  * Keepalived, HAProxy
  * Load-Balance Algorithm : Round-Robin, Least-Connection

## Network Programming
  * Socket
  * HTTP
  * Netty
  * SOAP
  * RESTFul
  * NIO

#### References
 * [JAVA NIO의 ByteBuffer와 Channel로 File Handling에서 더 좋은 Perfermance 내기!](http://eincs.com/2009/08/java-nio-bytebuffer-channel-file/)

## Spring Framework

  * 제어의 역행(IoC : Inversion of Control)
  * DI (Dependence Injection)
  * POJO ( Plain Old Java Object )

### Strength
  * Context로부터 DI받은 target객체를 client가 호출하면 실제적으로는 context가 target 객체를 제어함
  * 위와 같은 IoC 덕분에, Spring의 다양한 기능을 client 소스를 수정하지 않고 누릴 수 있음
  * 예를들어 AOP를 설정하면 context가 target 객체를 호출하기 전/후에 AOP를 수행해주도록 처리됨
  * AOP : Transaction, Controller Request/Response Mapping, Async(Easy Multi Threading), Scheduling 등 
  * Annotation으로도 대부분의 설정이 가능

  * DI로 제공한 target 객체의 생명주기는 기본적으로 singleton
  * 매번 인스턴스를 새로 생성하려면 component에다가 @Scope("prototype")으로 설정필요

### @Autowired, @Resource 차이
  * @Resource 는 기본적으로 프로퍼티 이름을 가지고 빈을 소환
    - @Resource(name="multiLanguageSource") 처럼 @Bean(name="multiLanguageSource") 라고 name이 설정된 bean 을 불러올 수 있음
    - 프로퍼티 혹은 setter method만 사용가능 : 파라미터가 하나인 setter
  * @Autowired 기본적으로 프로퍼티 타입을 가지고 빈을 소환 
    - 프로퍼티, 생성자, 모든 method에서 사용가능
    - 프로퍼티의 타입만으로 제어가 힘들 때는 @Qualifier("value")를 bean과 client에 함께 적용하면 됨

## J2EE
  * Servlet 의 생명주기 
  * [filter & interceptor (Spring) ](http://changpd.blogspot.kr/2013/03/spring.html)
  * web.xml

## Middleware
  * WEB : Apache, Nginx, WebToB
  * WAS : Tomcat, Jeus

## Object-Oriented Programming
객체와 객체의 상호작용을 통해 프로그램이 동작

a. 객체지향 프로그래밍은 코드의 재사용성이 높다.  
b. 코드의 변경이 용이  
c. 직관적인 코드분석  
d. 개발속도 향상  
e. 상속을 통한 장점 극대화  

## Aspect-Oriented Programming
트랜잭션이나 로깅, 보안

## 진행한 프로젝트에서의 부딪혔던 문제사항들과 해결방법 정리

 자소서 참고


# 추가 준비사항

  * [Java 데이터형의 메모리 크기](http://aventure.tistory.com/59)
  * [비트연산](http://secretroute.tistory.com/entry/%EC%9E%90%EB%B0%94%EC%9D%98%E7%A5%9E-Vol1-%EB%B9%84%ED%8A%B8-%EC%8B%9C%ED%94%84%ED%8A%B8-%EC%97%B0%EC%82%B0%EC%9E%90)


