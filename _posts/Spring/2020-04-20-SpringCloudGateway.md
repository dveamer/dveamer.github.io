---
layout: post
title: "Spring Cloud Gateway - Resilience4j, Kubernetes"
date: 2020-04-20 00:00:00
lastmod: 2020-04-24 00:00:00
categories: BackEnd
tags: BackEnd Spring MSA
---

![https://spring.io/](https://spring.io/images/spring-logo-9146a4d3298760c2e7e49595184e1975.svg){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

Spring Cloud Gateway의 기본적인 설정 그리고 circuit breaker, retry 설정들을 살펴봅니다.  
그리고 Kubernetes 환경에서는 [Spring Cloud Kubernetes](https://cloud.spring.io/spring-cloud-static/spring-cloud-kubernetes/1.1.2.RELEASE/reference/html/)를 이용하고 로컬환경에서는 [Netflix Eureka](https://cloud.spring.io/spring-cloud-static/spring-cloud-netflix/2.2.2.RELEASE/reference/html/)를 이용하는 방법을 소개합니다.  


<!--more-->

이 글의 주 핵심 내용은 gateway에다가 circuit breaker, retry GatewayFilter를 적용한 내용과 그렇게 설정한 목적입니다.  
근데 그것을 테스트하기 위해 Eureka를 세팅하고 마이크로 서비스들을 세팅하는 등 부수적인 내용이 많이 담기면서 글이 좀 길어졌습니다. 많은 양해 부탁드립니다.  

# 작업환경

제가 작업했던 환경은 아래와 같습니다. 필수적인 환경 조건은 아닙니다.  

  * Ubuntu 18.04 LTS
  * JDK 11.0.7 ( JDK 8 이상은 확실하게 필요합니다. )
  * Gradle 5.6.4

# 사전작업

설명드릴 소스와 테스트를 위한 소스들을 GitHub [SpringCloudGatewayDemo](https://github.com/dveamer/SpringCloudGatewayDemo)에서 내려 받아주시기 바랍니다.  

~~~terminal
$ mkdir -p ~/workspace/SpringCloudGatewayDemo
$ cd ~/workspace/SpringCloudGatewayDemo
$ git clone https://github.com/dveamer/SpringCloudGatewayDemo.git .
$ ls -al
~~~

gateway, eureka, service 라는 3개의 디렉토리가 보이실 겁니다.  

gateway는 앞으로 설명드릴 내용의 결과물이라고 보시면 됩니다.  

eureka, service의 경우는 gateway를 테스트하기 위해서 연동될 대상입니다.  

# Spring Cloud Gateway 설정

## build.gradle 설정

2019.11.28 에 Spring Cloud Hoxton.RELEASE 가 출시됐습니다.  

Spring Blog의 [spring-cloud-hoxton-released](https://spring.io/blog/2019/11/28/spring-cloud-hoxton-released) 공지에 나와있듯이 Hoxton.RELEASE 버전은 Spring Boot 2.2.1.RELEASE 버전을 기반으로 돌아갑니다.  

> Spring Cloud Hoxton.RELEASE is based on Spring Boot 2.2.1.RELEASE.  
> [spring-cloud-hoxton-released](https://spring.io/blog/2019/11/28/spring-cloud-hoxton-released)  

즉, 프로젝트의 Spring Boot 버전을 2.2.1.RELEASE 로 맞춰야 빌드가 가능하고 큰 탈 없이 진행 가능합니다.  


```build.gradle```

~~~gradle
plugins {
    id 'org.springframework.boot' version '2.2.1.RELEASE'
    id 'io.spring.dependency-management' version '1.0.9.RELEASE'
    id 'java'
}

group = 'com.dveamer'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '11'

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.springframework.boot:spring-boot-starter-actuator'
    testImplementation('org.springframework.boot:spring-boot-starter-test') {
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
    }

    implementation 'org.springframework.cloud:spring-cloud-starter-gateway'

    // Service Discovery
    implementation 'org.springframework.cloud:spring-cloud-kubernetes-discovery'
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-eureka-client'

    // Circuit Breaker
    implementation 'org.springframework.cloud:spring-cloud-starter-netflix-hystrix'
    implementation 'org.springframework.cloud:spring-cloud-starter-circuitbreaker-reactor-resilience4j'
}

dependencyManagement {
    imports {
        mavenBom 'org.springframework.cloud:spring-cloud-dependencies:Hoxton.RELEASE'
    }
}

test {
    useJUnitPlatform()
}
~~~

### dependencies

  * 기본 의존성
    - spring-boot-starter
    - spring-boot-starter-actuator

  * Gateway
    - spring-cloud-starter-gateway

  * Service Discovery  
    - spring-cloud-kubernetes-discovery 
      : Kubernetes master node ETCD API 호출을 통해 service discovery를 해주는 라이브러리로써 로컬환경에서는 off 시켜야 합니다.  
    - spring-cloud-starter-netflix-eureka-client
      : Eureka API 호출을 통해 service discovery를 해주는 라이브러리로써 Kubernetes 환경에서는 off 시켜야합니다.  

  * Circuit Breaker
    - spring-cloud-starter-netflix-hystrix
      : 불필요합니다. 하지만 GatewayCircuitBreakerAutoConfiguration에 버그가 있어서 함께 의존성 추가를 해줘야 동작합니다. 최신 버전에서는 수정되었다고 합니다. - [issue](https://github.com/spring-cloud/spring-cloud-gateway/issues/1445)
    - spring-cloud-starter-circuitbreaker-reactor-resilience4j
      : resilience4j 중에서도 reactor 버전입니다. Hystrix과 거의 흡사한 기능을 제공합니다.  

## GatewayApplication.java

```com.dveamer.gateway.GatewayApplication```

~~~java
package com.dveamer.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;

@EnableDiscoveryClient
@EnableCircuitBreaker
@SpringBootApplication
public class GatewayApplication {
	public static void main(String[] args) {
		System.setProperty("reactor.netty.http.server.accessLogEnabled", "true");
		SpringApplication.run(GatewayApplication.class, args);
	}
}
~~~

  * @EnableDiscoveryClient : service discovery의 auto configuration이 동작하도록 설정합니다.  
  * @EnableCircuitBreaker : circuit breaker의 auto configuration이 동작하도록 설정합니다.  
  * accessLogEnabled : acess log를 남기기 위한 작업 중 하나로 [Access Log 설정](#Access Log 설정)에서 다시 설명합니다.  

### dependencyManagement

spring-cloud-dependencies에 대해서 Hoxton.RELEASE 로 기술된 버전들을 사용하겠다고 정의했다.  

## application.yml

~~~yaml
server:
  port: 8080

management:
  endpoints:
    web:
      exposure:
        include: "*"

logging:
  level:
    root: WARN
    com.dveamer.gateway: DEBUG
    reactor.netty.http.server.AccessLog: INFO


spring:
  application:
    name: gateway
  cloud:
    loadbalancer:
      ribbon:
        enabled: false
    kubernetes:
      enabled: false
    gateway:
      httpclient:
        connect-timeout: 1000
        response-timeout: 1000ms
      routes:
        - id: service
          uri: lb://service-a
          predicates:
            - Path=/**
~~~

로컬환경, Kubernetes 환경에서 공통적으로 사용하게 될 프로퍼티 정보입니다.  
```application-kubernetes.yml```에서 동일한 프로퍼티 값을 세팅했다면 Kubernetes환경에서는 ```application-kubernetes.yml```의 값을 따라 갑니다. 하지만 ```application-kubernetes.yml```에 없는 프로퍼티 값이 있다면 Kubernetes환경에서도 ```application.yml``` 값을 따라가게 됩니다.  

```application.yml```을 ```application-kubernetes.yml```로 엎어 쓴다고 생각하시면 편합니다.  

### server.port

Gateway 서비스가 기동되면서 listen하게 될 port 정보입니다.  

### management.endpoints

Spring Actuator 관련 설정 정보입니다. 현재는 모든 현황을 볼 수 있도록 설정되어있지만 보안상 취약하므로 필요에 맞게 설정하셔야 합니다.  
Kubernetes 환경에서도 그렇고 Eureka를 쓸 때도 그렇고 Actuator를 이용한 health check는 굉장히 유용합니다.  

### spring.cloud.loadbalancer.ribbon

Netflix에서 제공하는 ribbon 대신 ReactorLoadBalancerExchangeFilterFunction 사용하기 위해서 ribbon 기능을 disable 시켰습니다.  

### spring.cloud.kubernetes

로컬환경 테스트를 위해 kubernetes 관련 기능을 disable 시켰습니다.  
```build.gradle``` 에서 spring-cloud-kubernetes-discovery 의존성을 추가했지만 여기서 disable 시켜뒀기 때문에 로컬환경에서 service discovery시에 우리는 Eureka를 사용하게 됩니다.  
반대로 Kubernetes 환경을 위해서 ```application-kubernetes.yml``` 파일에서는 enable로 설정하면 됩니다.  

### spring.cloud.gateway.httpclient

Spring Cloud Gateway는 기본적으로 HTTP 콜에 대해서 [Apache Http Client](https://hc.apache.org/httpcomponents-client-ga/) 혹은  [OkHttp](https://square.github.io/okhttp/) 모듈을 이용해서 backend 서비스로 연결시켜줍니다.  
그 중 HttpClient가 기본이고 설정을 통해 OkHttp로 변경이 가능하지만 여기서는 HttpClient로 진행하겠습니다.  

Spring Cloud Gateway에서 사용하는 모든 HttpClient의 기본 connect-timeout과 response-timeout을 각가 1초로 설정했습니다. 각 라우팅 별로 timeout을 설정할 수도 있습니다.  
각 timeout을 1초로 설정한 이유는 이 글이 가진 목적의 테스트를 용이하게 하기 위해서입니다. 운영환경에서는 각 환경에서 적절한 값을 찾아서 넣으셔야 합니다.  


### spring.cloud.gateway.routes

```routes``` 설정을 보면 Spring Cloud Gateway를 호출한 모든 URI(```/**```)에 대해서 service-a라는 이름의 서비스로 라우팅 합니다.  
```redicates```에 정의된 룰에 해당되는 요청이 들어오면 mapping된 ```uri```로 라우팅 시켜줍니다.  
그리고 해당 서비스가 여러 instance라면 부하를 분산시키겠다는 의미로 ```uri```에 ```lb://```를 적어주시면 됩니다.  

만약 ```/service-a/**```, ```/service-b/**``` 와 같이 두개 이상의 서비스가 필요하다면 아래와 같이 설정하면 됩니다.  


~~~yaml
...(생략)

spring:
  application:
    name: gateway
  cloud:
    ...(생략)
    gateway:
      ...(생략)
      routes:
        - id: service-a
          uri: lb://service-a
          predicates:
            - Path=/service-a/**
        - id: service-b
          uri: lb://service-b
          predicates:
            - Path=/service-b/**
~~~

보시면 모든 URI(```/**```)가 아닌 분리된 path로 설정된 것을 보실 수 있습니다. 반드시 path로 라우팅해야하는 것은 HTTP header, HTTP method 등 다양한 방법을 가지고 라우팅하실 수 있고 그 부하분산의 weight도 지정할 수 있습니다. 자세한 내용은 [spring-cloud-gateway 공식 가이드](https://cloud.spring.io/spring-cloud-gateway/reference/html)를 참고하시기 바랍니다.  

참고로 service-a 를 service_a 와 같이 under bar(_)로 설정하면 gateway 기동시 에러가 납니다. 대신 bar(-)를 사용하시면 되고 이는 각 서비스의 spring.apllication.name과 관련되기 때문에 서비스 이름을 정할 때 주의하셔야하는 사항입니다.  


## application-resilience4j.yml

~~~yaml
spring:
  cloud:
    gateway:
      default-filters:
      - CircuitBreaker=myCircuitBreaker
      - name: Retry
        args:
          retries: 3
          series: # empty
          methods:
          - GET
          - POST
          - PUT
          - DELETE
          exceptions:
          - java.net.ConnectException
~~~

spring.cloud.gateway에 filter를 두개 설정했습니다. 그리고 그 순서는 상당히 중요합니다.  

  * CircuitBreaker : resilience4j에서 제공하는 기본 circuit breaker 기능을 사용하게 됩니다. 자세한 내용은 [Circuit Breaker 테스트](#Circuit Breaker 테스트)에서 다시 다루겠습니다.  
  * Retry : client의 요청을 서비스로 전달하고 응답을 client에게 전달하는 과정에서 특정 실패가 발생하면 재시도를 하게 해줍니다. 상세한 설명은 [HA 테스트](#HA 테스트)에서 다시 다루겠습니다.  

그리고 이 설정 내용은 applciation.yml에 합치셔도 상관없습니다. 제가 파일을 나눈 이유는 retry가 설정되지 않은 상태의 테스트 진행과 설정된 상태의 테스트 진행 결과를 비교시켜드리기 위해 나눠둔 것입니다.  


## application-hystrix.yml


~~~yaml
spring:
  cloud:
    gateway:
      default-filters:
      - name: Hystrix
        args:
          name: fallbackcmd
          fallbackUri: forward:/fallback
      - name: Retry
        args:
          retries: 3
          series: # empty
          methods:
          - GET
          - POST
          - PUT
          - DELETE
          exceptions:
          - java.net.ConnectException

hystrix:
  command:
    default:
      execution.isolation.thread.timeoutInMilliseconds: 100000
      circuitBreaker:
        errorThresholdPercentage: 1
        requestVolumeThreshold: 1
        sleepWindowInMilliseconds: 10000
      metrics.rollingStats.timeInMilliseconds: 10000
~~~

resilience4j 가 아닌 hystrix를 사용하게 될 때 설정을 어떻게 해야하는지 보여드리기 위해서 생성해둔 파일입니다. 실제로는 불필요 합니다.  


## application-kubernetes.yml


~~~yaml
spring:
  application:
    name: gateway
  cloud:
    kubernetes:
      enabled: true
      ribbon:
        mode: POD
    gateway:
      default-filters:
        - CircuitBreaker=myCircuitBreaker
        - name: Retry
          args:
            retries: 3
            series: # empty
            methods:
              - GET
              - POST
              - PUT
              - DELETE
            exceptions:
              - java.net.ConnectException
~~~

Kubernetes 환경에서 사용하게 될 프로퍼티 정보입니다.  

```application.yml```과 차이점은 spring.cloud.kubernetes 를 enable 시켰다는 점과 ```application-resilience4j.yml```에서 봤던 filter 등록을 동일하게 해뒀다는 점입니다.  

그리고 spring.cloud.kubernetes.ribbon.mode를 기본 값인 POD로 설정했습니다. 이것을 SERVICE로 바꾸게되면 Kubernetes의 service를 이용하게 됩니다. Service는 Kubernetes에서 제공하는 일종의 load-balancer로써 readness에 의해 가용여부가 체크된 POD로만 연결되기 때문에 retry GatewayFilter는 불필요해 집니다.  


## bootstrap-kubernetes.yml

Kubernetes 환경에서 Eureka를 disble 시키기 위해 필요합니다.  

~~~yaml
eureka:
  client:
    enabled: false
~~~

## bootstrap.yml

Eureka와 연동하기 위한 설정입니다.  
만약 Eureka를 localhost에 기본 port(8761)로 띄웠다면 사실 bootstrap.yml은 추가하실 필요 없습니다.  
아래 설정 내용이 기본 설정과 동일하기 때문에 굳이 bootstrap.yml파일을 생성해서 아래 설정을 하지 않더라도 Spring Cloud Gateway를 기동시키면 자동으로 Eureka와 연동합니다.  

불필요하지만 bootstap-kubernetes.yml 과 비교하기 위하여 기본 설정이라도 명시적으로 입력했습니다.  

~~~yaml
eureka:
  client:
    enabled: true
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
    healthcheck:
      enabled: true
~~~


## Access Log 설정

[reactor-netty-access-logs](https://cloud.spring.io/spring-cloud-gateway/reference/html/#reactor-netty-access-logs) 에서 설명하는대로 ```reactor.netty.http.server.accessLogEnabled``` 의 값을 ```true```로 세팅하면 됩니다.  

~~~properties
reactor.netty.http.server.accessLogEnabled=true
~~~

주의하실 사항은 Spring 프로퍼티가 아니기 떄문에 application.yml에 설정하셔도 반영되지 않습니다.  

저의 경우는 아래와 같이 GatewayApplicatoon.main() 메소드에서 시스템 프로퍼티 설정을 직접 진행했습니다.  


```com.dveamer.gateway.GatewayApplication```

~~~java
...(생략)
@SpringBootApplication
public class GatewayApplication {
	public static void main(String[] args) {
		System.setProperty("reactor.netty.http.server.accessLogEnabled", "true");
		SpringApplication.run(GatewayApplication.class, args);
	}
}
~~~


그 뒤에는 ```reactor.netty.http.server.AccessLog``` class 파일에 대해서 로그 설정을 하시면 됩니다.  

```application.yml```

~~~yaml
logging:
  level:
    root: WARN
    com.dveamer.gateway: DEBUG
    reactor.netty.http.server.AccessLog: INFO
~~~

그리고 호출해보면 아래와 같은 형태로 access log가 남는 것을 확인할 수 있습니다.  

~~~terminal
2020-04-23 18:18:37.193  INFO 13124 --- [or-http-epoll-2] reactor.netty.http.server.AccessLog      : 127.0.0.1 - - [23/Apr/2020:18:18:36 +0900] "GET / HTTP/1.1" 200 36 8080 551 ms
~~~

SLF4J로 되어있으니 LogBack, Log4j2 등 사용하시는 로그 프로그램에 따라 설정하셔서 로그 패턴도 수정하실 수 있습니다.  

## 기동 테스트 

실행해서 정상적으로 기동되는지 체크합니다.  

~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/gateway
$ gradle bootRun
~~~


# Eureka / Micro Services

Spring Cloud Gateway를 로컬환경에서 동작시켜보기 위해서는 Eureka와 최종적으로 연동될 micro services가 필요합니다.  
제가 미리 준비해둔 Eureka와 Services를 기동시켜주시기 바랍니다.  

[사전작업](#사전작업)이 먼저 진행 되어있어야 합니다.  

## Micro Services 기동

1개의 마이크로 서비스지만 인스턴스를 3개 띄울 예정입니다. 
부하분산, HA(High Availibility), circuit breaker 기능 등을 확인하기 위해서 입니다.  

동일한 서비스임에도 불구하고 ```spring.profils.active```를 분리해서 다른 설정을 가지고 띄웠습니다.  
그 이유는 첫번째는 로컬환경이라서 포트(port)가 충돌나기 때문이고 두번째는 테스트를 위해 각각 다른 동작을 하도록 하기 위함입니다.  
실제로 사용할 때는 보통 환경별로(로컬,개발,운영) ```spring.profils.active```를 구분해서 사용합니다. 이런 식으로 같은 환경내에서 동작을 다르게 하기 위해서 사용하지는 않습니다.  

### service01

신규 터미널 창(단축키 : ```Ctrl+Alt+T```)을 열고 아래의 명령어를 입력합니다.  

~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/service
$ SPRING_PROFILES_ACTIVE=service01 gradle bootRun
or
$ bash start.sh service01
~~~

9001 포트로 기동되며 ```/``` 호출에는 200 성공 응답을 줍니다.  

~~~terminal
$ curl -i localhost:9001/
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:00:51 GMT

{"msg":"Hello world from service01"}
~~~

```/exception``` 호출에도 200 성공 응답을 줍니다.  

~~~terminal
$ curl -i localhost:9001/exception
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:06:30 GMT

{"msg":"Success from service01"}
~~~

### service02

신규 터미널 창(단축키 : ```Ctrl+Alt+T```)을 열고 아래의 명령어를 입력합니다.  

~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/service
$ SPRING_PROFILES_ACTIVE=service02 gradle bootRun
or
$ bash start.sh service02
~~~

9002 포트로 기동되며 ```/``` 호출에는 200 성공 응답을 줍니다.  

~~~terminal
$ curl -i localhost:9002/
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:00:51 GMT

{"msg":"Hello world from service02"}
~~~

```/exception``` 호출에는 200 성공 응답을 주지만 10초간 대기 후 응답을 줍니다.  
지금은 직접 호출해서 성공응답을 받았지만 gateway에서는 1초의 response-timeout이 설정되어있기 때문에 gateway를 통해서 응답을 요청하면 timeout에러가 발생합니다.  

~~~terminal
$ curl -i localhost:9002/exception
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:03:36 GMT

{"msg":"Success from service02"}
~~~

### service03

신규 터미널 창(단축키 : ```Ctrl+Alt+T```)을 열고 아래의 명령어를 입력합니다.  

~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/service
$ SPRING_PROFILES_ACTIVE=service03 gradle bootRun
or
$ bash start.sh service03
~~~

9003 포트로 기동되며 ```/``` 호출에는 200 성공 응답을 줍니다.  

~~~terminal
$ curl -i localhost:9003/
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:00:51 GMT

{"msg":"Hello world from service03"}
~~~

```/exception``` 호출에는 500 서버에러 응답을 줍니다.  

~~~terminal
$ curl -i localhost:9003/exception
HTTP/1.1 500 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Thu, 23 Apr 2020 11:00:55 GMT
Connection: close

{"msg":"/ by zero"}
~~~

## Eureka 기동

신규 터미널 창(단축키 : ```Ctrl+Alt+T```)을 열고 아래의 명령어를 입력합니다.  
  
~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/eureka
$ gradle bootRun
~~~

그리고 브라우저에서 ```http://localhost:8761```에 접속해보시면 "Instances currently registered with Eureka" 라고 적힌 곳 아래에 GATEWAY, SERVICE-A가 보이실 겁니다. Status는 UP인 상태이고 GATEWAY는 1개, SERVICE-A는 3개의 IP:port가 출력되어있습니다. 만약에 제가 알려드린 정보와 다르게 출력된다면 좀 더 기다렸다가 새로고침(F5)를 해보시면 변경되어있을 겁니다.  

모든 service-a 인스턴스들이 Eureka에 등록됐고 gateway는 이미 그 정보를 받아가서 discovery를 위한 준비가 완료됐을 것입니다.  

# 부하분산 테스트 

부하분산에 대한 설정은 이미 위에서 적용되어있습니다.  

Eureka를 통해 service discovery를 하도록 설정되었고 ```spring.cloud.gateway.routes[0].uri``` 에 ```lb://```로 설정해뒀기 때문에 Eureka에 등록된 service-a 인스턴스가 여러개 있다면 gateway는 부하분산 처리를 합니다.  

아래의 호출을 여러번 실행해보시면 응답하는 인스턴스가 달라지는 것을 확인할 수 있습니다.  

~~~terminal
$ curl localhost:8080
{"msg":"Hello world from service01"}

$ curl localhost:8080
{"msg":"Hello world from service02"}

$ curl localhost:8080
{"msg":"Hello world from service03"}
~~~

# 테스트 

## HA 테스트 

인스턴스 3개 중 1개를 종료하고 계속 호출하면 3번 중 1번은 에러가 발생합니다.  

~~~terminal
$ curl -i localhost:8080
HTTP/1.1 500 Internal Server Error
Content-Type: application/json
Content-Length: 200

{"timestamp":"2020-04-23T11:32:00.026+0000","path":"/","status":500,"error":"Internal Server Error","message":"finishConnect(..) failed: Connection refused: /192.168.0.18:9003","requestId":"460a6921"}
~~~

HA 제공에 실패한 경우라고 보시면 됩니다.  

인스턴스 1개는 종료되었지만 Eureka가 그 사실을 인지하는데도 시간이 걸리고 gateway가 Eureka에게 최신 정보를 갱신 받는데도 시간이 걸립니다.  
시간이 지나고 최종적으로 정보가 갱신되면 에러는 발생하지 않고 종료된 인스턴스에는 호출이 가지 않습니다.  


Eureka server, client 설정을 설정해서 걸리는 시간을 줄일 수는 있습니다. 하지만 그에 따른 다른 비용이 발생하고 그렇다고 완벽한 HA를 보장할 수 있는 방법이 아니기 때문에 추천되지 않습니다.  

더 좋은 해결방법은 실패했을 경우는 다른 인스턴에 재시도를 하도록 만드는 것입니다.  

gateway 인스턴스를 종료하고 spring.profiles.active를 resilience4j 값으로 설정해서 다시 기동합니다.  

~~~terminal
$ cd ~/workspace/SpringCloudGatewayDemo/gateway
$ SPRING_PROFILES_ACTIVE=resilience4j gradle bootRun
or
$ bash start.sh resilience4j
~~~

그리고 서비스 인스턴스를 3개 모두 기동 상태로 하여 부하분산이 고르게 들어가는 것을 확인 한 후 인스턴스 1개를 종료해서 재 테스트 해봅니다.  
아까와 같은 에러가 발생하지 않습니다. 이제 HA가 보장되었고 무중단 배포와 같은 것도 시도해볼 수 있게 됐습니다.  

새로 기동하면서 spring.profiles.active를 resilience4j 값으로 설정함으로써 ```application-resilience4j.yml``` 프로퍼티 정보가 ```application.yml```와 함께 사용되도록 설정됐습니다.  

```application-resilience4j.yml```  

~~~yml
spring:
  cloud:
    gateway:
      default-filters:
      - CircuitBreaker=myCircuitBreaker
      - name: Retry
        args:
          retries: 3
          series: # empty
          methods:
          - GET
          - POST
          - PUT
          - DELETE
          exceptions:
          - java.net.ConnectException
~~~

추가된 설정정보를 살펴보면 ```Retry``` 라는 필터를 default-filters에 추가했습니다.  

재시도 요청은 위험이 따릅니다. 예를들어, POST같은 데이터를 입력하는 호출인 경우, client 입장에서는 첫번째 시도가 실패여서 재시도를 했는데 server 입장에서는 첫번째 호출이 오래걸렸지만 아직 처리 중이었다고 한다면 2회 이상의 데이터 입력이 발생할 수 있습니다.  

그래서 이런 경우가 발생하지 않도록 HTTP connection 성공된 건에 대해서는 어떠한 상황에서도 재시도를 하지 않고 connection이 실패한 건에 대해서만 재시도를 하도록 설정했습니다.  

### series

```series```에는 재시도를 할 HTTP status 메시지 리스트를 적게 되는데 이것은 비워놨습니다. [RetryGatewayFilterFactory](https://github.com/spring-cloud/spring-cloud-gateway/blob/master/spring-cloud-gateway-core/src/main/java/org/springframework/cloud/gateway/filter/factory/RetryGatewayFilterFactory.java) 소스를 살펴보면 series의 기본값이 5xx 에러로 되어있습니다. 기본 값으로 하게 되면 문제가 발생할 가능성이 있습니다. 예를들어, 복잡한 DB transaction을 다루게 되면 에러가 났음에도 불구하고 입력이 되는 경우가 있을 수 있기 때문입니다. 이에 대한 재시도를 gateway가 기본설정으로 가져가는 것은 문제가 있다고 생각됩니다. 그래서 series를 빈값으로 설정했습니다.  

### exceptions

```exceptions```에는 ```java.net.ConnectionException```만 넣었습니다. Connection 관련 에러가 발생하면 재시도하도록 설정했습니다.  

### retries

호출 실패시 몇회까지 다른 인스턴스를 호출 해볼 것인지를 정하는 값입니다. 3으로 해뒀지만 운영환경에 맞게 적절한 값을 넣으시면 됩니다.  

### methods

```methods```는 기본적으로 GET만 처리되도록 되어있지만 모든 호출에 대해서 처리할 수 있도록 세팅했습니다.  

## Circuit Breaker 테스트 

Gateway 입장에서의 circuit breaker 사용목적은 단순합니다.  
문제가 있다고 판단된 API로는 일시적으로 라우팅되지 않도록 해주고 client에게는 일시적으로 사용불가하다는 에러 메시지를 내보냅니다.  

Circuit breaker는 API별, 상황별로 적절한 응답을 내보낼 수 있고 그 덕분에 문제 발생시에도 마치 문제가 없는 것처럼 기존에 캐싱된 데이터로 응답할 수도 있습니다.  
하지만 gateway에서 모든 케이스에 대해서 설정하는 것은 문제가 있습니다. 그러한 세세한 설정은 각 서비스에서 처리해야하는 사항이고 gateway에서는 문제가 생긴 API로는 더 이상 부하가 들어가지 않도록 차단해서 장애가 전파되지 않도록 막는 것이 주된 목적입니다.  


~~~yml
spring:
  cloud:
    gateway:
      default-filters:
      - CircuitBreaker=myCircuitBreaker
      - name: Retry
        args:
          retries: 3
          series: # empty
          methods:
          - GET
          - POST
          - PUT
          - DELETE
          exceptions:
          - java.net.ConnectException
~~~

```CircuitBreaker=myCircuitBreaker``` 를 filter로 추가하는 것만으로 gateway를 위한 Resilience4j에서 제공하는 기본적인 circuit breaker 기능을 사용할 수 있습니다. 하지만 그 기본적인 기능이 굉장히 합리적입니다.  
여기서 circuit breaker에 의해서 카운팅되는 에러 케이스는 특정 라우팅을 하는 과정에서 gateway 내에서 발생한 에러입니다. 즉, 서비스에서 에러응답을 보낸 것에 대해서는 카운팅하지 않습니다. 앞서 이야기 드렸듯이 서비스에서 발생하는 에러에 대한 메시지는 그대로 client에게 제공됩니다. 만약 에러 메시지가 아닌 좀 더 매끄러운 응답을 제공하고 싶다면 각 서비스에서 매끄러운 응답을 만들어서 제공하도록 하면 됩니다. 다만, gateway의 가용성에 영향을 준다거나 다른 서비스들에 영향을 주는 장애전파를 막기 위한 기능을 제공합니다.  

예를들면, 주로 해당되는 케이스는 오래걸리는 API입니다. 설정해둔 response-timeout 보다 응답이 오래걸리면 timeout 에러가 발생되고 처리되지만 그런 케이스가 굉장히 많다면 response-timeout의 시간만큼 기다리는 것도 gateway의 가용성에도 영향을 줄 수 있습니다. 또한 뒷단의 서비스들의 가용성에도 영향을 주게 됩니다.  


### API 살펴보기

미리 만들어둔 ```/excpetion``` API를 호출하면 앞서 [Micro Services 기동](#Micro Services 기동) 과정에서 봤듯이 서비스의 인스턴스별로 응답이 다르게 해뒀습니다.  


가장 먼저 첫번째 인스턴스는 성공 메시지를 응답합니다.  

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 200 OK
transfer-encoding: chunked
Content-Type: application/json
Date: Thu, 23 Apr 2020 08:06:46 GMT

{"msg":"Success from service01"}
~~~

두번째 인스턴스는 10초 대기 후 성공메시지를 주지만 gateway에서는 response-timeout을 1초로 설정해뒀기 때문에 504(gateway timeout) 에러가 발생합니다.  
이것은 Resilience4j가 에러로 카운팅을하고 빈번하게 발생하여 일정 수준을 넘기게 되면 circuit을 open 상태로 변경합니다.  

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 504 Gateway Timeout
Content-Type: application/json
Content-Length: 183

{"timestamp":"2020-04-22T18:28:25.773+0000","path":"/exception","status":504,"error":"Gateway Timeout","message":"Response took longer than configured timeout","requestId":"5f368dc3"}
~~~


세번째 인스턴스는 고의적인 500(internal server error)를 발생시켰고 그에 대한 에러 응답을 제공합니다.  
하지만 이것은 Resilience4j가 에러로 카운팅하지 않습니다. 이러한 에러 처리는 각 서비스에서 처리하면 되는 내용입니다.  

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 500 Internal Server Error
transfer-encoding: chunked
Content-Type: application/json
Date: Thu, 23 Apr 2020 08:09:33 GMT

{"msg":"/ by zero"}
~~~


만약에 모든 인스턴스가 기동되지 않은 상태라면 아래와 같은 에러가 발생합니다.  
이것은 Resilience4j가 에러로 카운팅을하고 빈번하게 발생하여 일정 수준을 넘기게 되면 circuit을 open 상태로 변경합니다.  

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 503 Service Unavailable
Content-Type: application/json
Content-Length: 178

{"timestamp":"2020-04-23T08:06:13.943+0000","path":"/exception","status":503,"error":"Service Unavailable","message":"Unable to find instance for service","requestId":"7fbc5466"}
~~~

### Circuit Open

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 503 Service Unavailable
Content-Type: application/json
Content-Length: 186

{"timestamp":"2020-04-22T18:28:57.214+0000","path":"/exception","status":503,"error":"Service Unavailable","message":"Upstream service is temporarily unavailable","requestId":"eef85e2c"}
~~~

반복적으로 호출하다보면 갑자기 에러 코드가 503(temporarily unavailable)로 변경됩니다. Open 상태로 변경됐다는 의미입니다.  

현재는 최소 5번 호출이 발생 한 뒤에 1번이라도 에러가 발생하면 open 상태로 빠지도록 설정되어있습니다. 그리고 20초 후에 half-open 상태가 되고 2번 이상 성공하면 closed 상태가 되고 만약에 또 실패한다면 다시 open 상태가 됩니다.  

이 수치는 설정을 통해서 적절히 바꿔둬야 합니다. 현재는 테스트를 용이하게 하기 위해서 굉장히 쉽게 open 상태로 빠지도록 설정해둔 상황입니다.  
yaml 파일로 설정을 하지 않았고 java configuration 을 이용해서 설정했습니다. 그 이유는 [겪었던 삽질 - Resilience4j Configuration](#Resilience4j Configuration)을 참고해주시기 바랍니다.  

```com.dveamer.gateway.Resilience4jConfig```

~~~java
package com.dveamer.gateway;

import io.github.resilience4j.circuitbreaker.CircuitBreakerConfig;
import org.springframework.cloud.circuitbreaker.resilience4j.ReactiveResilience4JCircuitBreakerFactory;
import org.springframework.cloud.circuitbreaker.resilience4j.Resilience4JConfigBuilder;
import org.springframework.cloud.client.circuitbreaker.Customizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.time.Duration;

@Configuration
public class Resilience4jConfig {

    @Bean
    public Customizer<ReactiveResilience4JCircuitBreakerFactory> defaultCustomizer() {
        CircuitBreakerConfig circuitBreakerConfig = CircuitBreakerConfig.custom()
                .failureRateThreshold(5)
                .permittedNumberOfCallsInHalfOpenState(2)
                .slidingWindowSize(2)
                .minimumNumberOfCalls(5)
                .waitDurationInOpenState(Duration.ofMillis(20000))
                .build();
        return factory -> factory.configureDefault(id -> new Resilience4JConfigBuilder(id)
                .circuitBreakerConfig(circuitBreakerConfig)
                .build());
    }
}
~~~

  * [Resilience4j Configuration - Circuit Breaker](https://resilience4j.readme.io/docs/circuitbreaker)  
    - [YAML Sample](https://resilience4j.readme.io/docs/getting-started-3#section--configuration-)  



재미있는 점은 에러 메시지도 "Unable to find instance for service, "Upstream service is temporarily unavailable" 처럼 보기 좋게 변경된다는 점입니다. 저는 개인적으로 사용자에게 출력되는 에러메시지는 화면에서 재처리해야한다고 생각하기 떄문에 앞서 제공됐던 에러 메시지 정도면 충분하지 않을까 싶습니다. Gateway 관점에서 circuit breaker의 주된 역할은 에러메시지를 꾸미는 것이 아니라 "라우팅하는 과정에서 발생가능한 장애전파를 막는 것이다" 라는 것을 다시 강조해봅니다.  

만약 에러 종류별로 에러 메시지를 다르게 바꾸고 싶다면,  
fallback 을 전달받을 Controller를 하나 만드시고 ```application.yml``` 혹은 ```application-resilience4j.yml``` 등에 설정된 circuit breaker 설정을 변경해야 합니다.  

```com.dveamer.gateway.FallbackController```  

~~~java

@RestController
public class FallbackController {

    Logger logger = LoggerFactory.getLogger(this.getClass());

    @GetMapping("/fallback")
    public Mono<String> fallback(ServerWebExchange exchange) {
        Throwable exception = exchange.getAttribute(ServerWebExchangeUtils.CIRCUITBREAKER_EXECUTION_EXCEPTION_ATTR);
        logger.debug("", exception);
        return Mono.just("fallback-gateway");
    }

}
~~~


```application-resilience4j.yml```  

~~~yaml
spring:
  cloud:
    gateway:
      default-filters:
      - name: CircuitBreaker
        args:
          name: fetchIngredients
          fallbackUri: forward:/fallback
      #- CircuitBreaker=myCircuitBreaker
      ...(생략)
~~~

위와 같이 설정하면 Throwable을 얻을 수 있고 그것을 가지고 적절한 에러메시지를 만들어서 응답으로 보낼 수 있습니다.  

Throwable을 로그로 찍어보면 아래와 같이 정상적으로 출력되는 것을 확인할 수 있습니다.  

~~~terminal
2020-04-24 21:20:44.825 DEBUG 5688 --- [     parallel-1] com.dveamer.gateway.FallbackController   : 

java.util.concurrent.TimeoutException: Did not observe any item or terminal signal within 1000ms in 'circuitBreaker' (and no fallback has been configured)
        at reactor.core.publisher.FluxTimeout$TimeoutMainSubscriber.handleTimeout(FluxTimeout.java:288) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.publisher.FluxTimeout$TimeoutMainSubscriber.doTimeout(FluxTimeout.java:273) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.publisher.FluxTimeout$TimeoutTimeoutSubscriber.onNext(FluxTimeout.java:390) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.publisher.StrictSubscriber.onNext(StrictSubscriber.java:89) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.publisher.FluxOnErrorResume$ResumeSubscriber.onNext(FluxOnErrorResume.java:73) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.publisher.MonoDelay$MonoDelayRunnable.run(MonoDelay.java:117) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.scheduler.SchedulerTask.call(SchedulerTask.java:68) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at reactor.core.scheduler.SchedulerTask.call(SchedulerTask.java:28) ~[reactor-core-3.3.0.RELEASE.jar:3.3.0.RELEASE]
        at java.base/java.util.concurrent.FutureTask.run(FutureTask.java:264) ~[na:na]
        at java.base/java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:304) ~[na:na]
        at java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1128) ~[na:na]
        at java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:628) ~[na:na]
        at java.base/java.lang.Thread.run(Thread.java:834) ~[na:na]

2020-04-24 21:20:44.855  INFO 5688 --- [or-http-epoll-2] reactor.netty.http.server.AccessLog      : 127.0.0.1 - - [24/Apr/2020:21:20:43 +0900] "GET /exception HTTP/1.1" 200 16 8080 1184 ms
~~~

그리고 지금은 "fallback-gateway" 로 하드코딩 해뒀지만 원하는 에러 메시지가 client에게 전달되는 것을 확인했습니다.  

~~~terminal
$ curl -i localhost:8080/exception
HTTP/1.1 200 OK
Content-Type: text/plain;charset=UTF-8
Content-Length: 16

fallback-gateway
~~~


# 겪었던 삽질

## Spring Initializr 사용불가

2020.04.20 기준으로 [Spring Initializr](https://start.spring.io/)에서 Spring Cloud 관련 dependencies를 포함한 프로젝트 생성이 불가능합니다.  

[Spring Initializr](https://start.spring.io/)는 Spring Boot 프로젝트를 쉽게 시작할 수 있게 도와주는 사이트로써 사용하고자하는 dependencies를 검색해서 선택만하면 버전정보도 맞춰서 프로젝트를 생성해줍니다. 우리는 다운로드해서 사용만 하면 됩니다.  

근데 이 사이트에서 Spring Boot 버전을 선택할 때 총 6개 중 1개를 선택할 수 있는데 그 6개가 가장 최근에 진행 중인 버전이 출력됩니다.  
그러다보니 Spring Boot 2.2.1.RELEASE를 선택할 수 없게 되고 Spring Cloud 관련된 dependencies를 검색할 수 없는 상황이 생겼습니다.  

분명 2019년도 12월에는 프로젝트 생성하면서 Spring Cloud for Kubernetes 라는 라이브러리가 검색이 됐었는데  
2020년 4월 20일에는 검색이 안되서 "내가 뭘 잘못했을까?" 를 한참 고민했으나 다행히 금방 [spring-cloud-hoxton-released](https://spring.io/blog/2019/11/28/spring-cloud-hoxton-released) 문서를 찾아서 [Spring Initializr](https://start.spring.io/)에서 기본 Spring Boot 프로젝트를 생성하고 Spring Boot 버전을 2.2.1.RELEASE로 수정했습니다. 그 후 Spring Cloud dependencies 추가는 직접 진행했습니다.  

## Release Candidate 버전

Spring Cloud Hoxton.RELEASE 출시 직전에는 Hoxton.RC1 버전을 사용해서 프로젝트를 진행 중이었습니다.  
RC는 "Release Candidate" 의 약자로 RELEASE 버전이 될 가능성이 높은 베타버전으로 큰 버그가 새로 발견되지 않으면 출시가 될 버전이라고 생각하시면 됩니다.  
RC1 버전을 사용하다가 출시가 되면 RELEASE로 교체할 계획을 갖고 있었습니다.  

근데 재미있는 일이 발생했습니다.  

Spring Cloud Hoxton.RELEASE 출시(2019.11.28) 후 [Maven Repository](https://mvnrepository.com/artifact/org.springframework.cloud/spring-cloud-dependencies)에서 Hoxton.RC1 버전이 사라져버렸습니다. 공식 Maven Repository에 원래부터 없었는지 모르겠지만 로컬 메이븐 레파지토리(```.m2```)를 초기화하고 ```gradle build```를 하면 dependencies를 찾을 수 없다는 에러 메시지가 출력됐습니다.  

"그럼 뭐 어때? 로컬 메이븐 레파지토리에 이미 캐싱되어 있으니 현재 개발중인 features 마무리하고 RELEASE로 버전을 올립시다." 하고 프로젝트를 계속 진행하려고 했는데 문제가 생겼습니다. Spring Cloud 관련 다른 라이브러리를 추가로 사용해야하는 상황이었는데 해당 의존성을 추가하고나니 에러가 발생했습니다. Hoxton.RC1와의 의존성 관계를 확인해서 자동으로 적절한 버전으로 처리되야 하는데 Hoxton.RC1의 정보가 Maven Repository에서 사라져버려서 진행이 안된 것으로 추측하고 있습니다.  

결국은 features 개발을 마무리하지 못하고 먼저 RELEASE로 버전을 올리는 작업을 진행을 해서 해결했고  
특수한 상황이 아니고서는 RELEASE 버전만 사용하는 것이 정신건강에 이롭다는 교훈을 얻었습니다.  

## Resilience4j Configuration

Resilience4j가 동작하는 것은 확인이 어렵지 않았는데 application.yaml에서 관련 설정값들을 변경해도 반영되지 않았습니다.  
Resilience4jConfig 파일을 만들어서 java configuration으로 처리하니 정상적으로 반영되어 동작하는 것을 확인했습니다.  


제가 사용한 Resilience4j의 버전이 최신버전이 아니여서 발생한 문제로 추측 중입니다. Spring Cloud Hoxton.RELEASE 버전에 맞추다보니 2019.11.28 시기에 출시되어있던 Resilience4j를 사용했습니다. 당시 Resilience4j의 완성도가 낮은 상태였고 프로퍼티에 의한 설정은 적용이 안되어있는 것 같습니다. 아마도 지금 최신 버전의 Resilience4j를 사용하면 정상적으로 반영되리라 생각합니다.  


