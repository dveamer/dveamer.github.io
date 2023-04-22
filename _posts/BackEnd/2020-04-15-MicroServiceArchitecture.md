---
layout: post
title: "MSA Micro Service Architecture - PAPI (Permissions API), Spring Cloud Gateway"
date: 2020-04-15 00:00:00
categories: BackEnd
tags: BackEnd MSA
---

![인증 & 인가](/images/post_img/PAPI/PAPI_02.png)  

[Kubernetes](https://kubernetes.io) 위에서 [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway) 와 같은 Spring Cloud 계열의 service mesh를 사용해서 구축한 경험을 공유합니다.  

또한 Spring Cloud Gateway와 [PAPI]() 조합을 이용해서 API 별 인증(Authentication) / 인가(Authorization) 처리한 내용을 공유합니다.  

<!--more-->


# Spring Cloud를 이용한 이유는?

Istio를 이용하면 혹은 Kubernetes만으로도 service mesh는 해결 가능합니다.  
그런데 Spring Cloud를 이용해서 service mesh를 구현한 이유는  

Kubernetes, Istio에 대한 경험, 자신감 부족과  
"Spring Cloud는 어자피 Java, Spring 인데 뭐.." 라는 근거 없는 자신감(?)도 있었지만  
가장 큰 이유는 프로젝트 상황이었습니다.  

## 프로젝트 상황

VM 환경과 Kubernetes 환경 중 실제로 어디서 프로젝트를 구성할지 정해지지 않았던 상황이었습니다.  
또한 Kubernetes로 진행한다고 하더라도 PKS(Pivotal Container Service), RedHat OpenShift, GCP GKE(Google Kubernetes Engine), AWS EKS(Elastic Kubernetes Service) 중 어떤 것을 선택하게 될지 모르는 상황이었습니다.  

일단 개발은 시작해야했고 Spring Cloud를 이용해서 개발하면 VM 혹은 어떤 Kubernetes 위에서도 큰 변경없이 동작이 가능하니 선택했습니다.  
덕분에 빠르게 service mesh, 인증/인가 등에 대해서 설계를 빠르게 시작할 수 있었습니다.  

만약 처음부터 Kubernetes에 종속되게 설계했거나 Istio를 사용하도록 설계했는데 막판에 어떠한 다른 이유로 Kubernetes를 사용하지 않는 결정이 일어난다면 굉장히 난감했을 수도 있습니다.  
혹은 Istio 몇버전 이상을 기반으로 설계했는데 PKS, OpenShift, GKE, EKS 에서 제공하는 Kubernetes의 버전과 호환성 문제가 있다면 난감했을 수도 있습니다.  
환경에 대한 결정을 쉽게할 수 있으면 참 좋겠지만 그 결정에는 굉장히 많은 변수들이 있고 제가 혹은 제 동료가 할 수 있는 것이 아니였기 때문에 Spring Cloud 로 선택했다고 보시면 됩니다.  

Kubernetes, Istio에 대한 확실한 믿음이 없는 이 시기가 그런 상황으로 보여집니다.  
참고로 제가 설명하는 프로젝트는 2019년도 말에 시작했습니다.  

# 구성도 

![구성도](/images/post_img/PAPI/PAPI_01.png)


## Actors

3종류의 actor가 있습니다.  

  * Other Systems : 인증과정을 거친 타 시스템에서의 접근  
  * User : 브라우저, 모바일, IoT 등에서 접근하는 로그인한 사용자  
  * Public Access : 인증과정 없는 접근 (브라우저, 타 시스템)  

그리고 사내 내부망과 외부망 각각 3종류의 actor가 모두 존재합니다.  
참고로 외부망은 우리가 집에서 사용하는 인터넷 망입니다.  

## GCP GKE

최종 적으로는 GCP GKE가 선택됐습니다.  

어떤 플랫폼을 사용하느냐에 따라 영향을 받는 것은 꽤나 많습니다.  
비용, 네트워크, VPC, CI/CD, 모니터링, 로깅, Kafka, DB, NoSQL 등 Kubernetes 밖에서 구성되는 모든 것은 영향을 받습니다.  

하지만 이 글에서는 그러한 내용을 다루지 않습니다. 이 글에서 플랫폼의 종류는 큰 상관이 없습니다.  
그냥 Kubernetes를 이용했고 GCP에서 제공하는 가상 로드밸런서를 이용했구나 라고 생각해주시면 됩니다.  

로드밸런서는 내부망, 외부망을 위해 2개 세팅되어있습니다.  
그리고 로드밸런서에 SSL 인증서를 등록해서 사용했습니다.  
참고로 HTTP 프로토콜 외 다른 통신 프로토콜은 사용한 것이 없습니다.  

## Inbound Spring Cloud Gateway 

LB에 등록된 Kubernetes 진입점을 제한해서  
Kubernetes 안으로 들어오는 모든 inbound 호출은 Spring Cloud Gateway를 통하게 했습니다.  
그 덕분에 Spring Cloud Gateway 인증처리 1회 첫번째 micro service에서 인가처리 1회 후  
Spring Cloud Gateway 뒷단의 각 micro service간의 호출은 인증, 인가 처리가 필요 없어집니다.  

[인증 & 인가](# 인증 & 인가)에 대한 내용은 아래서 다시 다루겠습니다.  


그 외에 inbound gateway에서 처리하는 주된 내용은 아래 3개가 있다.  

  * Service discovery
  * Load balance & high availability
  * Circuit breaker

[Spring Cloud Gateway - Resilience4j, Kubernetes](/backend/SpringCloudGateway.html) 글을 보시면 Spring Cloud Gateway에 대한 기본적인 세팅방법과 위 3가지 요소에 대해서 어떻게 설정했는지를 알 수 있습니다.  


<!--ads-->

## Outbound Spring Cloud Gateway

Micro service가 Kubernetes 외부의 다른 시스템과 연동을 할 때 outbound gateway를 거치게 했습니다.  
Outbound gateway는 Spring Cloud Gateway를 forward proxy 형태로 사용했습니다.  
HAProxy, Nginx, Apache HTTPD 전부 forawrd proxy로 사용했던 것처럼 Spring Cloud Gateway도 사용해본 것입니다.  
다만 HAProxy, Nginx, Apache HTTPD에서 뭔가 새로운 작업을 하기 위해서는 각 solution이 제공하는 스크립트를 이용해야 했습니다.  
예를들어, lua script 같은 언어로 프로그래밍해야합니다. 개인 프로젝트할 때는 몇번 해봤지만 대부분 Java 프로그래머들로 구성된 회사 프로젝트에서는 쉽지 않습니다. "운영은 어떻게 할거냐?" 라는 질문부터 받게 됩니다. 하지만 이제는 Spring Cloud Gateway가 있으니 그럴 필요가 없습니다. Spring Cloud Gateway의 filter 기능을 이용해서 Java로 구현하시면 쉽게 해결됩니다.  

보통 forward proxy의 사용목적은 라우팅과 캐싱 두가지 일것입니다.  
Outbound gateway의 목적은 2가지 이지만 라우팅과 캐싱은 아니였습니다.  

첫째는 tracing입니다. Zipkin을 써서 tracing을 할 때 외부 서비스에 대한 접근시도 여부도 체크가 가능해집니다.  

둘째는 외부 시스템과 연동시 필요한 인증과정이 쉬워집니다.  
외부 시스템과 연동 할때 예전에는 방화벽으로만 제한하는 시스템들이 많았지만 이제는 대부분 L7 레벨에서 인증과정을 거칩니다.  
(방화벽 등록도 간단해질 것처럼 보이지만 어자피 각 마이크로 서비스는 public IP를 가지고 있지 않고 NAT IP로 나가도록 되어있기 때문에 원래 간단합니다.)  

단순하게 모든 마이크로 서비스에서 인증과정을 구현하는 방법이 있습니다.  
이건 초반에 구현은 쉽지만 외부시스템 혹은 micro service 종류가 늘어날 수록 변경사항에 대해 대응이 어려워지는 방법입니다.  
외부 시스템의 개수가 N개라고하고 micro service 종류의 수가 M개라고 한다면  
최악의 경우, N개의 토큰 생성, 관리 로직을 M개의 micro service에 동일하게 구현해야할 수 있습니다.  
예를들어, 어떤 외부 시스템의 토큰 생성하는데 필요한 secret 값을 정기적으로 바꿔야한다면 각 서비스별로 바꿔줘야 할 것입니다.  

다른 방법으로는 토큰 생성, 관리를 대신해주는 서비스를 별도로 만드는 것입니다.  
각 마이크로 서비스는 해당 서비스로부터 받은 토큰을 삽입해서 외부 시스템과 연동하면 됩니다.  
그렇게 되면 변경이 있어도 토큰 생성, 관리해주는 서비스에서만 변경하면 됩니다.  

저희가 선택한 세번째 방법으로는 outbound gateway에서 토큰 생성, 관리 그리고 삽입을 처리합니다.  
각 마이크로 서비스는 인증과정은 신경쓰지 않고 outbound gateway만 호출하면 외부 시스템과 인증을 거쳐서 업무처리가 진행됩니다.  

네번째 방법으로는 outbound gateway에서 별도의 토큰 생성, 관리 시스템에게 요청해서 토큰을 받은 다음에 outbound gateway는 토큰 삽입만 해주는 것입니다.  
하지만 외부시스템 종류도 많지 않았고 불필요함을 느껴서 세번째 방법을 이용했습니다. 하지만 outbound gateway에서만 분리해내면 되기 때문에 필요시 쉽게 분리 가능합니다.  

이런 내용을 설명하고나니 꼰대같지만 이 이야기를 하지 않을 수 없습니다.  

Java 개발을 하면서 특정 객체에 특정 책임과 역할을 주고 다른 객체는 그 세부 내용은 알수없도록 숨기고 메시지로만 요청과 결과를 주고받는 것이 OOP인 것처럼  
마이크로 서비스별로 책임과 역할을 주고 다른 서비스는 그 세부내용을 모르도록 설계하게 되고  
결국 MSA는 OOP를 마이크로 서비스 단위로 하는 것이라는 생각이 듭니다.  

하지만 OOP가 옳다고 생각해서 그렇게 하는 것이 아니라  
어떻게 하면 더 유연한 구조를 갖춰서 추후에 생길 변경사항에 대해서 덜 영향을 받을지 고민하는 과정에서 같은 결과가 나오는 것입니다.  

물론 MSA에 더 많은 내용이 있겠지만 그 것은 패스하도록 하겠습니다.  

그리고 참고로 Outbound gateway를 거쳐서 외부 시스템과 연동하는 것이 필수 사항은 아닙니다. 예를들어, 방화벽 체크만하는 경우는 그럴 필요가 없습니다.  


<!--ads-->

# 인증 & 인가

![인증 & 인가](/images/post_img/PAPI/PAPI_02.png)

위의 이미지에 설명을 적어놨지만 어설픈 영어로 적어놨으니 한글로 공손하게 다시 적어보겠습니다.  
이미지의 숫자와 매칭해서 보시면 됩니다.  

  1. Gateway 인증처리  
    * 요청을 받은 gateway는 HTTP header로부터 JWT를 추출합니다.  
    * 그리고 JWT의 변조여부를 체크 합니다.  
    * 변조되지 않은 JWT에서 user_id, user_roles를 추출합니다.  
    * 만약 JWT가 존재하지 않는다면, user_id, user_roles 대신에 미리 정해둔 public_id, public_role를 사용합니다.  
    * 만약 JWT가 변조된 것으로 확인되면, user_id, user_roles 대신에 미리 정해둔 public_id, public_role를 사용합니다.  

  2. Gwateway 요청받은 호출을 서비스로 라우팅 하면서 HTTP header에 user_id, user_roles 그리고 G/W에서 호출했다는 의미로 from_which라는 key에 GW라는 값을 넣어서 보냅니다.  

  3. PAPI checker는 호출받은 마이크로 서비스의 controller method에 선언되어있는 @API_NAME annotation에 입력된 api_name값을 찾아냅니다.  

  4. PAPI checker는 호출받은 api_name에 대해서 접근가능한 역할 리스트를 PAPI server에 질의합니다.  

  5. PAPI checker 인가 처리  
    * PAPI chekcer는 PAPI server로부터 받은 해당 api_name에 접근가능한 역할 리스트와 JWT에서 추출한 user_roles를 비교합니다.  
    * 만약 두 리스트 사이에서 공통집합이 없다면, 해당 요청은 권한이 없는 요청으로 판단되어 401 HTTP status code로 응답합니다.  
    * 공통잡합이 있다면 권한이 있는 요청으로 판단되어 controller method를 처리할 수 있도록 해줍니다.  
    * 만약 접근가능한 역할 리스트 중에 public_role이 있다면 항상 권한이 있는 요청으로 판단됩니다.  

  6. user_id, user_roles 전달  
    * 마이크로 서비스 1의 controller method가 실행되는 과정에서 마이크로 서비스 2가 호출되는 경우가 있다면 user_id, user_roles 그리고 from_which(MS1)을 HTTP header에 담아 전달합니다.  

여기서 PAPI Server, PAPI Checker는 인증/인가를 위해 만든 라이브러리로 JAR 형태로 제공됩니다. 참고로 PAPI는 Permissions API의 약자입니다.  

## Gateway

위의 설명으로 인증 & 인가 과정에서 gateway의 역할을 설명이 되었을 것이라고 생각합니다.  
JWT의 변조여부를 체크하고 라우팅할 때 추출한 사용자 정보를 HTTP hedaer에 넣어서 보낸다는 2가지 작업입니다.  

이 두가지 작업은 Spring Cloud Gateway가 제공하는 filter 기능을 이용해서 구현했습니다.  

### Spring Cloud Gateway vs Kubernetes Ingress Gateway

이런 질문을 가질 수 있습니다. Kubernetes Ingress Gateway를 사용하지 않고 Spring Cloud Gateway를 사용했을까?  

사실 앞서 한번 이미 답변을 한 내용이긴 합니다.  

일단 Spring Cloud Gateway에서 제가 이용한 기능들은 Ingress도 구성 가능하고 더 다양한 기능을 가지고 있습니다. 다시 말해서, Ingress를 사용했어도 문제 없습니다.  

하지만 프로젝트 상황상 어떤 플랫폼 위에서 Kubernetes를 사용하게 될지 혹은 Kubernetes를 사용할지 말지조차 정해지지 않은 상황이었습니다.  
Ingress라는 것은 추상적인 개념이고 실제 구현체는 [GLBC](https://github.com/kubernetes/ingress-gce/blob/master/README.md), [NGINX Ingress Controller](https://github.com/kubernetes/ingress-nginx/blob/master/README.md)), [F5 BIG-IP Controller](https://clouddocs.f5.com/products/connectors/k8s-bigip-ctlr/v1.5/), [Kong Ingress Controller](https://konghq.com/blog/kubernetes-ingress-controller-for-kong/) 등 다양하게 존재합니다. 그리고 정확하진 않지만 RedHat OpenShift는 HAProxy기반으로 Ingress가 구현되어있는 것으로 알고 있습니다.  

Spring Cloud Gateway를 선택하면 어느 플랫폼에서든 사용가능했기 때문에 선택했다고 보시면 됩니다.  

### 인증과정에서 Gateway가 반드시 필요한가? 

JWT 변조여부를 체크하고 사용자 정보를 추출하는 기능을 PAPI checker에서 직접 진행한다면 gateway는 단순히 라우팅만 해도 되는것 아닌가? 라는 의문이 생길 수 있습니다.  
그렇게 하지 않은 이유는 단순합니다. 토큰 변조여부를 체크하기 위해서는 토큰의 secret을 알고 있어야 합니다. 그 secret을 모든 서비스에 제공하고 싶지 않았습니다.  


## PAPI (Permissions API)

PAPI 에 대한 구첵적인 설명은 아직 작성 중입니다.  




