---
layout: post
title:  "Spring Boot Configuration & Kubernetes ConfigMap: OS 환경변수 바인딩"
date:   2021-07-25 00:00:00
lastmod: 2021-09-04 00:00:00
categories: BackEnd
tags: Spring SpringBoot Properties Kubernetes
---

![https://spring.io/](https://upload.wikimedia.org/wikipedia/commons/thumb/4/44/Spring_Framework_Logo_2018.svg/200px-Spring_Framework_Logo_2018.svg.png){:class="imgTitle"} ![https://kubernetes.io/](https://upload.wikimedia.org/wikipedia/commons/thumb/3/39/Kubernetes_logo_without_workmark.svg/247px-Kubernetes_logo_without_workmark.svg.png){:class="imgTitle"}  
( Image reference : [https://upload.wikimedia.org](https://upload.wikimedia.org) ) 


이 글은 OS 환경변수를 Spring Boot의 프로퍼티에 바인딩하는 방법을 다룹니다.  
그리고 그 방법을 이용해서 Kubernetes configmap을 Spring Boot 프로퍼티에 바인딩 하는 방법도 다룹니다.  

이를 이용해서 프로퍼티를 더 간단하고 유연하게 관리 가능합니다.  
로컬환경에 필요한 프로퍼티는 **application.yml**로 관리하면되고  
개발환경, 운영환경 등에 필요한 프로퍼티는 각 OS 환경변수 혹은 Kubernetes configmap을 활용해서 관리합니다.  

<!--more-->

이와 같은 방법을 이용하면 Spring Boot, Spring Cloud의 기본적으로 설정된 정보들도 환경별로 다르게 설정할 때도 소스코드 수정 없이 쉽게 할 수 있습니다.  

  * [Spring Boot, Spring Cloud 설정정보 모음](/backend/PropertiesOfSpringSolutions.html)


## Spring Boot Externalized Configuration

Spring Boot는 아래와 같은 외부 PropertySource들을 이용해서 설정할 수 있습니다.  
여기서 중요한 점은 아래 항목의 역순을 우선순위로 하여 프로퍼티에 적용하게 됩니다.  
**5. OS environment variables**가 **3. Config data (such as application.properties files)** 보다 우선순위가 높다 점만 확인해주시면 이 글을 이해하실 수 있습니다.  

> Spring Boot uses a very particular PropertySource order that is designed to allow sensible overriding of values. Properties are considered in the following order (with values from lower items overriding earlier ones):  
>  
> 1. Default properties (specified by setting SpringApplication.setDefaultProperties).  
> 2. @PropertySource annotations on your @Configuration classes. Please note that such property sources are not added to the Environment until the application context is being refreshed. This is too late to configure certain properties such as logging.* and spring.main.* which are read before refresh begins.  
> 3. Config data (such as application.properties files)  
> 4. A RandomValuePropertySource that has properties only in random.*.  
> 5. OS environment variables.  
> 6. Java System properties (System.getProperties()).  
> 7. JNDI attributes from java:comp/env.  
> 8. ServletContext init parameters.  
> 9. ServletConfig init parameters.  
> 10. Properties from SPRING_APPLICATION_JSON (inline JSON embedded in an environment variable or system property).  
> 11. Command line arguments.  
> 12. properties attribute on your tests. Available on @SpringBootTest and the test annotations for testing a particular slice of your application.  
> 13. @TestPropertySource annotations on your tests.  
> 14. Devtools global settings properties in the $HOME/.config/spring-boot directory when devtools is active.  
> 출처 : [https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config)  


## 환경별 설정 관리

결론적으로 이야기하면, 로컬 환경은 config data(예: **application.properteis**, **application.yml**)를 이용해서 설정하고 개발, 운영과 같은 배포 환경은 OS 환경변수를 이용해서 Spring Boot 기동시점에 설정정보를 주입합니다.  

이 방법의 장점을 이해하기 위해 몇가지 다른 방법들을 함께 살펴보겠습니다.  

### 환경별 설정정보 소스 레벨 관리

git과 같은 형상관리에 **appliation-production.yml**과 같은 운영환경의 설정정보를 보관하게 됩니다.  

git에 접근 가능한 모든 사용자에게 운영환경 정보가 노출되기 때문에 보안 위험이 있습니다. 
하지만 이런 설정정보의 개수가 많지는 않으므로 보통 예외적으로 별도 처리하고 이보다 더 큰 에로사항이 있습니다.  

운영환경의 설정을 바꾸기 위해서는 **appliation-production.yml** 파일을 수정하고 git에 반영 후 빌드, 배포하는 등의 CI/CD 전체 과정이 진행되야 한다는 점입니다.  

### 환경별 설정정보 빌드 시점 적용

**appliation-production.yml** 파일을 git에서 관리하지 않고 빌드 과정에서 **appliation-production.yml** 파일을 추가 후 빌드하여 진행하는 방식입니다.  

앞서 이야기 드렸던 보안 위험은 해결되겠지만 여전히 운영환경 설정 변경을 위해서는 빌드, 배포 과정이 필요하다는 점이 에로 사항입니다.  

### 환경별 설정정보 기동 시점 적용

OS 환경변수 바인딩을 이용해서 Spring Boot 기동 시점에 적용 합니다.  

설정정보 변경을 위해서 별도의 빌드 과정이 불필요 합니다. Docker와 같은 컨테이너를 활용하는 시스템이라면 테스트된 컨테이너 이미지를 개발, 운영 모두 동일하게 사용하는 것이 가장 권장되는 사용방법 일텐데 이런 컨셉과도 잘 맞아 떨어집니다.  

컨테이너를 활용하지 않고 VM에서 직접 기동하는 시스템이더라도 개발, 운영 환경에서 테스트 된 동일한 jar파일을 사용하는 것이 권장됩니다.  

대신 OS 환경변수를 관리하기 위한 별도의 관리체계가 필요해 집니다. MSA(Micro Service Architecture)와 같이 관리해야되는 OS가 여러개인 환경에서는 설정정보에 대한 형상관리, 배포자동화가 되지 않으면 관리가 너무 어렵습니다. 저의 경우에는 환경별 설정정보만 관리하는 Git 프로젝트를 만들었습니다. 여기서는 모든 환경별, 마이크로서비스별 설정값들을 관리합니다. 그리고 각 환경별, 마이크로서비스 별로 설정 정보를 OS 환경변수에 적용하도록 자동화 배포를 구성해뒀습니다.  

<!--ads-->

## OS 환경변수 바인딩 방법

사실 별다른 방법은 없습니다. Spring Boot에서 요구하는 문법에 맞춰서 OS 환경변수를 설정하기만 하면 됩니다.  


### 기본형 

**application.properties** 혹은 **application.yml** 파일에서 사용했던 설정값을 아래와 같은 3가지 규칙에 따라 변환해서 사용하기 만 하면 됩니다.  

> To convert a property name in the canonical-form to an environment variable name you can follow these rules:  
>   * Replace dots (.) with underscores (_).  
>   * Remove any dashes (-).  
>   * Convert to uppercase.  
> For example, the configuration property spring.main.log-startup-info would be an environment variable named SPRING_MAIN_LOGSTARTUPINFO.  
> 출처 : [https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config.typesafe-configuration-properties.relaxed-binding.environment-variables](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config.typesafe-configuration-properties.relaxed-binding.environment-variables)


예를들어, 아래와 같이 연동하는 타 업체의 URL을 관리하는 설정값이 있다고 하겠습니다.  

**application.properties**  

~~~properties
third-parties.kakao.url=https://kakao.com
third-parties.kakao.name=kakao
third-parties.naver.url=https://naver.com
third-parties.naver.name=naver
~~~


**application.yml**  

~~~properties
third-parties:
  kakao:
    url: https://kakao.com
    name: kakao
  naver:
    url: https://naver.com
    name: naver
~~~

이를 OS 환경변수로 바꾸게 되면 아래와 같습니다.  

~~~bash
THIRDPARTIES_KAKAO_URL=https://kakao.com
THIRDPARTIES_KAKAO_NAME=kakao
THIRDPARTIES_NAVER_URL=https://naver.com
THIRDPARTIES_NAVER_NAME=naver
~~~

### 리스트형 


만약에 리스트 형으로 관리하고 있었다면 **[0].** 을 **_0_** 으로 변환해주면 됩니다.  

> Environment variables can also be used when binding to object lists. To bind to a List, the element number should be surrounded with underscores in the variable name.  
> For example, the configuration property my.service[0].other would use an environment variable named MY_SERVICE_0_OTHER  
> 출처 : [https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config.typesafe-configuration-properties.relaxed-binding.environment-variables](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.external-config.typesafe-configuration-properties.relaxed-binding.environment-variables)


예를들어, 아래와 같이 연동하는 타 업체의 URL을 관리하는 설정값이 있다고 하겠습니다.  

**application.properties**  

~~~properties
third-parties[0].url=https://kakao.com
third-parties[0].name=kakao
third-parties[1].url=https://naver.com
third-parties[1].name=naver
~~~


**application.yml**  

~~~properties
third-parties:
  - url: https://kakao.com
    name: kakao
  - url: https://naver.com
    name: naver
~~~

이를 OS 환경변수로 바꾸게 되면 아래와 같습니다.  

~~~bash
THIRDPARTIES_0_URL=https://kakao.com
THIRDPARTIES_0_NAME=kakao
THIRDPARTIES_1_URL=https://naver.com
THIRDPARTIES_1_NAME=naver
~~~

<!--ads-->


## Kubernetes Configmap

Kubernetes configmap은 key-value 쌍의 데이터를 저장하는 API 오브젝트이고 POD는 configmap을 환경변수로 사용 가능합니다.  

> A ConfigMap is an API object used to store non-confidential data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume.  
> 출처 : [https://kubernetes.io/docs/concepts/configuration/configmap/](https://kubernetes.io/docs/concepts/configuration/configmap/)  
 
결국 Kubernetes configmap에 설정한 값을 OS 환경변수로 적용하고 그 값이 Spring Boot에 적용되는 간단한 내용입니다.  
POD 종류 그리고 개수가 엄청 많아도 configmap 설정만으로 각 application을 설정할 수 있게 됩니다.  

앞서 사용했던 기본형 예시를 다시 사용한다면 아래와 같이 configmap을 설정해주시면 됩니다.  

아래 yaml은 example-config 라는 이름의 configmap의 예시입니다.  

~~~yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
data:
  THIRDPARTIES_KAKAO_URL: https://kakao.com
  THIRDPARTIES_KAKAO_NAME: kakao
  THIRDPARTIES_NAVER_URL: https://naver.com
  THIRDPARTIES_NAVER_NAME: naver
~~~

그리고 **envFrom** 과 **configMapRef** 설정을 이용해서 configmap을 OS 환경변수 값으로 사용할 수 있습니다.  
한개만이 아닌 여러개의 confgiMapRef를 적용할 수 있기 때문에 configmap을 조합해서 사용하는 것도 가능합니다.  

아래 yaml은 pod설정을 통해 example-config 라는 이름의 configmap을 OS 환경변수로 적용하는 예시입니다.  
물론 pod가 아닌 deployment 설정을 통해서도 pod의 OS 환경변수로 적용 가능합니다.  

~~~yml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
    - name: example-container
      ...(생략)
      envFrom:
      - configMapRef:
          name: example-config
      ...(생략)
~~~


앞에서도 이야기 드렸지만, MSA와 같은 다양한 종류의 pod를 관리해야하는 시스템이라면 환경설정에 대한 형상관리, 배포 자동화가 되지 않으면 관리가 어렵습니다. 저의 경우에는 configmap만 관리하는 Git 프로젝트를 만들었습니다. 여기서는 모든 환경별, 마이크로서비스별 설정값들을 관리합니다. 그리고 각 환경별, 마이크로서비스 별로 configmap 자동화 배포를 구성해뒀습니다.  


