---
layout: post
title: "Spring Boot, Spring Cloud의 설정정보 모음"
date: 2020-02-08 00:00:00
lastmod: 2021-09-04 00:00:00
categories: BackEnd
tags: Spring SpringCloud SpringBoot Properties
---

![https://spring.io/](https://upload.wikimedia.org/wikipedia/commons/thumb/4/44/Spring_Framework_Logo_2018.svg/200px-Spring_Framework_Logo_2018.svg.png){:class="imgTitle"}  
( 이미지 출처 : [https://upload.wikimedia.org](https://upload.wikimedia.org) )  

Spring Boot, Spring Cloud의 설정정보들을 가진 공식 사이트 링크를 모아봤습니다.  

Spring Boot의 auto configuration을 통해 다양한 솔루션들을 쉽게 사용할 수 있습니다.  
튜토리얼 정도의 사용 수준에서는 입력할 설정정보가 몇개 안되지만 운영을 하는 과정에서는 그렇지 않습니다.  

솔루션들의 기능을 보다 더 잘 사용하기 위해서는 각각의 운영환경에 맞게 기본 설정값들을 변경할 필요가 생깁니다.  

<!--more-->

솔루션들의 공식 사이트는 우리가 어떤 설정할 수 있는지 그리고 그 default 값이 무엇인지 잘 정리해뒀습니다.

아래와 같이 ```application.properties``` 혹은 ```application.yml``` 파일에 입력함으로써 사용하시면 됩니다.  

```application.properties```

~~~properties
spring.task.execution.pool.allow-core-thread-timeout=true
spring.task.execution.pool.core-size=8
spring.task.execution.pool.keep-alive=60
...(생략)
~~~


## Spring Boot

  * [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html)
  * [Hikari DB Connection Pool](https://github.com/brettwooldridge/HikariCP#configuration-knobs-baby)

  * [Spring Boot Quartz](https://docs.spring.io/spring-boot/docs/2.0.0.M3/reference/html/boot-features-quartz.html)
    - Boot 관련 설정은 [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html) 에서 확인  
    - 그 외에 Quartz Scheduler configuration 을 변경하려면 ```spring.quartz.properties.``` 뒤에 Quartz 프로퍼티 정보를 넣어서 작성하면 된다.  
    - 예를들어, ```org.quartz.threadPool.threadCount``` 값을 설정하고 싶다면 ```spring.quartz.properties.org.quartz.threadPool.threadCount``` 에 입력하면 된다.  
    - 또는 ```SchedulerFactoryBeanCustomizer``` beans를 이용해서 programmatic하게 Quartz Scheduler configuration 을 변경 가능하다.  
    - [Quartz](http://www.quartz-scheduler.org/documentation/quartz-2.3.0/configuration/)
        

## Spring Cloud

  * [Gateway](https://cloud.spring.io/spring-cloud-gateway/reference/html/appendix.html)
  * [Open-Feign](https://cloud.spring.io/spring-cloud-openfeign/reference/html/appendix.html)
  * [Sleuth](https://cloud.spring.io/spring-cloud-sleuth/reference/html/appendix.html)
  * [Netflix Hystrix ](https://github.com/Netflix/Hystrix/wiki/Configuration#command-properties)
  * Resilience4j - [Sample](https://resilience4j.readme.io/docs/getting-started-3#section--configuration-)  
    - [Circuit Breaker](https://resilience4j.readme.io/docs/circuitbreaker)  
    - [Bulk Head](https://resilience4j.readme.io/docs/bulkhead)  
    - [Rate Limiter](https://resilience4j.readme.io/docs/ratelimiter)  
    - [Retry](https://resilience4j.readme.io/docs/retry)  
    - [Time Limiter](https://resilience4j.readme.io/docs/timeout)  
    - [Cache](https://resilience4j.readme.io/docs/cache)


  * [GCP Pub/Sub](https://docs.spring.io/spring-cloud-gcp/docs/1.2.6.RELEASE/reference/html/appendix.html)  
