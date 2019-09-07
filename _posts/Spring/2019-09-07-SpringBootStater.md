---
layout: post
title:  "Spring Boot Starter & Parent 로 간단히 의존성 설정하기"
date:   2019-09-07 00:00:00 
lastmod: 2019-09-07 00:00:00  
categories: BackEnd
tags: Spring Spring-Boot
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

굉장히 쉽고 빠르고 정확하게 의존성 설정을 할 수 있도록 도와주는 spring-boot-stater와 spring-boot-stater-parent에 대해서 설명합니다.  

<!--more-->

순수 Java 프로젝트는 필요한 라이브러리를 사용하기 위해선 라이브러리 파일들을 직접 보관하고 관리해야 했습니다. Maven, Gradle과 같은 프로젝트 관리 툴을 이용하면서 라이브러리의 정보, 버전, 의존성을 문서(pom.xml, build.gradle)에 작성만 해도 라이브러리를 사용할 수 있게 되었고 덕분에 관리와 공유도 쉬워졌죠. 하지만 여전히 라이브러리간의 의존성 관리와 버전 충돌은 우리들이 직접 해결하고 매번 작성해야 합니다.  

Java가 아닌 다른 언어에서는 이와 같은 문제를 어떻게 해결하고 있을까요?  
Python은 PIP라는 패키지 매니저를 통해 패키지를 설치하면 해당 패키지와 의존성을 갖는 패키지 조합을 사전에 정의되어있는 버전에 맞게 자동으로 설치해줍니다.  

하지만 프로젝트가 커지고 패키지 의존관계가 복잡해지다보면 PIP에 의한 설치과정에서도 버전 충돌이 종종 발생합니다. 그리고 이를 해결하는 것은 굉장히 까다롭습니다. PIP의 경우 해당 패키지를 배포한 개발자가 패키지 의존성, 버전 조합을 사전 정의를 하기 때문에 다양한 케이스에서 검증되지 못한 것은 당연합니다. 이러한 문제는 패키지 매니저를 가진 다른 언어들에서도 마찬가지 발생하는 문제일 것입니다.  

Spring 진영에서는 이러한 문제를 해결해주는 spring-boot-starter와 spring-boot-stater-parent를 제공했습니다. Spring Boot를 사용하면서 자주 사용하게 되는 라이브러리간의 의존성, 버전 조합을 각각 테스트한 뒤 공개했다고 보시면 됩니다. Spring 진영의 신뢰성 높은 검증을 거친 이 의존성 조합은 비록 세상 모든 라이브러리의 조합을 커버하지는 못하지만 자주 사용되는 라이브러리들에 대해서만큼은 스트레스를 받지 않고 설정할 수 있게 해줍니다.  

# Spring Boot Starter

Spring Boot에서는 spring-boot-starter라는 사전에 미리 정의한 편리한 의존성 조합을 제공합니다. 프로젝트에 설정해야하는 다수의 의존성들을 starter가 이미 포함하고 있기 때문에 우리는 starter에 대한 의존성 추가만으로도 프로젝트를 시작하거나 새로운 기능을 추가할 수 있습니다.  

```pom.xml```

~~~xml
<dependencies>
    ...(생략)

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
~~~


>Starters are a set of convenient dependency descriptors that you can include in your application. (...생략)  
>The starters contain a lot of the dependencies that you need to get a project up and running quickly and with a consistent, supported set of managed transitive dependencies.  
>참고자료 : https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter  

예를들어, 기존에 AOP를 이용하고 싶으면 org.springframework:spring-aop 의존성을 추가하고 사용하다가 aspectJ를 이용한 AOP를 구현하고 싶다면 org.aspectj:aspectjweaver 의존성을 추가해서 사용했었습니다. Spring Boot에선는 [spring-boot-starter-aop](https://github.com/spring-projects/spring-boot/blob/v2.1.7.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-aop/pom.xml) 의존성만 추가하면 이미 관련 의존성 조합이 한번에 추가되고 aspectJ를 사용하려고 한다면 @AspectJ 라는 annotation을 추가함으로써 사용 가능해집니다.  

의존성 조합의 간단한 예가 AOP였던 거고 좀 더 복잡한 의존성 조합을 보고 싶으시다면 다른 starter를 확인해 보시기 바랍니다. 예를들어, [spring-boot-starter-data-jpa v2.1.7.RELEASE](https://github.com/spring-projects/spring-boot/blob/v2.1.7.RELEASE/spring-boot-project/spring-boot-starters/spring-boot-starter-data-jpa/pom.xml)은 아래와 같이 7개의 의존성 조합을 포함하고 있고 잘 살펴보시면 그 7개의 의존성 중에 또 다른 spring-boot-starter가 존재해서 실제는 7개보다 더 많은 의존성이 설정되어있다고 보시면 됩니다. 또한 중복되어 문제가 되는 의존성에서대해서는 exclusion 설정도 되어있습니다.  
원래는 이 모든 의존성의 관계를 우리가 찾아서 설정해야하지만 미리 작성된 spring-boot-stater를 사용하면 쉽고 빠르고 정확하게 의존성 설정을 할 수 있습니다.  

```spring-boot-starter-data-jpa/pom.xml```

~~~xml
...(생략)
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-aop</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.transaction</groupId>
			<artifactId>javax.transaction-api</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.xml.bind</groupId>
			<artifactId>jaxb-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.hibernate</groupId>
			<artifactId>hibernate-core</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.jboss.spec.javax.transaction</groupId>
					<artifactId>jboss-transaction-api_1.2_spec</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-jpa</artifactId>
			<exclusions>
				<exclusion>
					<groupId>org.aspectj</groupId>
					<artifactId>aspectjrt</artifactId>
				</exclusion>
				<exclusion>
					<groupId>org.slf4j</groupId>
					<artifactId>jcl-over-slf4j</artifactId>
				</exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aspects</artifactId>
		</dependency>
</dependencies>
...(생략)
~~~


[Spring Docs - sping-boot-starter 페이지](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter) 혹은 [Github - spring-boot-stater v2.1.7.RELEASE 페이지](https://github.com/spring-projects/spring-boot/blob/v2.1.7.RELEASE/spring-boot-project/spring-boot-starters)를 보시면 spring에서 제공하는 starter 목록을 확인할 수 있습니다.  


# Spring Boot Starter Parent

프로젝트 시작 시기에 다양한 라이브러리들을 사용하게되면 라이브러리 버전간의 충돌문제가 발생할 수 있습니다. Sprign Boot의 starter가 의존성 조합을 제공해준다면 starter-parent는 해당 의존성 조합간의 충돌 문제가 없는 검증 된 버전정보 조합을 제공합니다.  

spring-boot-starter-parent 2.1.7.RELEASE에서 제공하는 버전정보 조합은 [Maven repository - spring-boot-stater-parent](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-parent/2.1.7.RELEASE) 혹은 [GitHub - spring-boot-stater-parent](https://github.com/spring-projects/spring-boot/blob/v2.1.7.RELEASE/spring-boot-project/spring-boot-parent/pom.xml) 에서 확인 가능합니다.  

우리는 아래와 같이 spring-boot-starter-parent 버전만 설정해도 수많은 라이브러리들의 버전충돌 문제를 피할 수 있습니다.  

```pom.xml```

~~~xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.7.RELEASE</version>
</parent>

<dependencies>
    ...(생략)

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
~~~


특정 의존성이 버전을 변경하고자 한다면 아래와 같이 properties를 이용해서 해당 의존성 버전을 override하면 됩니다.  

```pom.xml```

~~~xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.7.RELEASE</version>
</parent>

<properties>
    <aspectj.version>1.9.4</aspectj.version>
</properties>

<dependencies>
    ...(생략)

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
~~~
