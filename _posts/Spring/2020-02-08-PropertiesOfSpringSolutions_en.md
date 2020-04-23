---
layout: post_en
title: "All Properties Of Spring Boot, Spring Cloud"
date: 2020-02-08 00:00:00
lastmod: 2020-04-22 00:00:00
categories: BackEnd
tags: Spring SpringCloud SpringBoot
---

![https://spring.io/](https://spring.io/images/spring-logo-9146a4d3298760c2e7e49595184e1975.svg){:class="imgTitle"}  
( Image reference : [https://spring.io/](https://spring.io/) )  

Here's links that have the properties you need to use Spring Boot, Spring Cloud.  

<!--more-->

Various properties can be specified inside your ```application.properties``` file, inside your ```application.yml``` file, or as command line switches.  

```application.properties```

~~~properties
spring.task.execution.pool.allow-core-thread-timeout=true
spring.task.execution.pool.core-size=8
spring.task.execution.pool.keep-alive=60
...(skipped)
~~~

You can also check the default values of properties from the follow links.  

## Spring Boot

  * [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/appendix-application-properties.html)
  * [Hikari DB Connection Pool](https://github.com/brettwooldridge/HikariCP#configuration-knobs-baby)

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

