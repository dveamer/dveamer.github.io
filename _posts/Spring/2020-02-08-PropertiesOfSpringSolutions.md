---
layout: post
title: "Spring Boot, Spring Cloud의 설정정보 모음"
date: 2020-02-08 00:00:00
categories: BackEnd
tags: Spring Properties
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

Spring Boot, Spring Cloud의 설정정보들을 가진 공식 사이트 링크를 모아봤습니다.  

Spring Boot의 auto configuration을 통해 다양한 솔루션들을 쉽게 사용할 수 있습니다.  
튜토리얼 정도의 사용 수준에서는 입력할 설정정보가 몇개 안되지만 운영을 하는 과정에서는 그렇지 않습니다.  

솔루션들의 기능을 보다 더 잘 사용하기 위해서는 각각의 운영환경에 맞게 기본 설정값들을 변경할 필요가 생깁니다.  

<!--more-->

솔루션들의 공식 사이트는 우리가 어떤 설정할 수 있는지 그리고 그 기본값으 무엇인지 잘 정리해뒀습니다.

아래와 같이 ```application.properties``` 혹은 ```application.yml``` 파일에 입력함으로써 사용하시면 됩니다.  

```application.properties```

~~~properties
spring.task.execution.pool.allow-core-thread-timeout=true
spring.task.execution.pool.core-size=8
spring.task.execution.pool.keep-alive=60
...
~~~


## Spring Boot

  * [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html)
  * [Hikari DB Connection Pool](https://github.com/brettwooldridge/HikariCP#configuration-knobs-baby)

## Spring Cloud

  * [Gateway](https://cloud.spring.io/spring-cloud-gateway/reference/html/appendix.html)
  * [Open-Feign](https://cloud.spring.io/spring-cloud-openfeign/reference/html/appendix.html)
  * [Netflix Hystrix ](https://github.com/Netflix/Hystrix/wiki/Configuration#command-properties)
  * [Sleuth](https://cloud.spring.io/spring-cloud-sleuth/reference/html/appendix.html)

