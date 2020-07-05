---
layout: post
title:  "Spring Boot Auto Configuration 설정과 원리"
date:   2019-09-08 00:00:00 
lastmod: 2019-09-08 00:00:00  
categories: BackEnd
tags: Spring SpringBoot
---

![https://spring.io/](https://spring.io/images/spring-logo-9146a4d3298760c2e7e49595184e1975.svg){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

Spring Boot에서 제공하는 auto configuration을 설정하는 방법과 그 원리에 대해서 간략히 설명합니다.  

<!--more-->

Spring Boot는 Spring과 마찬가지로 component-scan을 통해 component들을 찾고 bean 생성을 진행합니다. 그 과정에서 우리가 설정한 bean들이 생성됩니다. 예를들면, @Controller, @RestController, @Service, @Repository 그리고 @Configuration에 등록한 @Bean 과 같은 설정들이죠. 그리고 그 과정에서 Spring Boot에서 미리 작성해둔 auto configuration에 의해 추가적인 bean들도 함께 생성됩니다.  

Spring에서는 ThreadPoolTaskExecutor를 사용하기 위해서는 우리가 해당 bean을 등록해야했지만 Spring Boot에서는 등록하지 않아도 해당 bean이 자동으로 생성되기 때문에 사용할 수 있게됩니다.  

# @EnableAutoConfiguration

@EnableAutoConfiguration은 auto configuration 기능을 사용하겠다는 설정입니다. @EnableAutoConfiguration을 설정하지 않는다면 auto configuration 을 사용하지 못하게 됩니다. 일반적으로 아래와 같이 @ComponentScan과 함께 사용됩니다.  

~~~java
package com.dveamer.sample

@SpringBootConfiguration
@ComponentScan("com.dveamer.sample")
@EnableAutoConfiguration
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
~~~

@ComponentScan에 입력된 com.dveamer.sample 값은 component scan를 시작할 패키지 위치입니다. com.dveamer.sample 하위 모든 패키지를 component scan 범위로 잡겠다는 설정입니다. package 위치를 입력하지 않는다면 com.dveamer.sample.Application이 놓여진 패키지(com.dveamer.sample)가 기본 값으로 사용됩니다. 여러 패키지 위치를 scan 대상으로 설정하는 것도 가능합니다.  

글의 시작점에서 설명했듯이 component scan을 통해서 모은 component들의 정보와 Spring Boot가 spring.factories 파일에 사전에 정의한 AutoConfiguration 내용에 의해 bean 생성이 진행됩니다.  

근데 만약에 우리가 정의한 bean과 Spring Boot가 정의한 bean이 충돌이 발생하면 어떻게 될까요? 혹은 AutoConfiguration의 종류가 굉장히 많은데 우리에게 필요한 설정은 몇개 되지 않는다면 쓸데없는bean이 생성되기도 하지 않을까요? 그리고 그 과정에서 등록되지 않은 라이브러리를 참조하면 문제가 발생할 수 있지도 않을까요?  

Spring Boot는 @Condition과 @Conditional을 이용해서 이와 같은 문제를 해결하여 AutoConfiguration 기능을 우리에게 제공합니다. 지금부터 그 내용을 다뤄보도록 하겠습니다.  

참고로 이 글의 하단에 @EnableAutoConfiguration와 관련된 아래와 같은 추가적인 정보가 있으니 참고하시기 바랍니다.  

  * [특정 AutoConfiguration 설정 제외하기](#Disabling Specific Auto-configuration Classes)
  * [@SpringBootApplication 설정](#@SpringBootApplication)


<!--ads-->

# Auto Configuration Filters & Conditions

Spring Boot가 미리 정의해둔 AutoConfiguration 정보는 ```spring-boot-autoconfigure/META-INF/spring.factories```에서 혹은 [spring.factories](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories)에서 확인 가능합니다.

```org.springframework.boot.autoconfigure.EnableAutoConfiguration```에 상당히 많은 AutoConfigruation이 등록되어있는 것을 확인할 수 있습니다.  

~~~properties
...(생략)

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
...(생략)
~~~

각 AutoConfigruation들은 필요한 상황에만 자신이 실행될 수 있도록 @Conditional, @Condition과 같은 annotation들로 설정이 되어있습니다. 그 annotation 을 기반으로 필터링이 먼저 이뤄지고 필터링되지 않은 AutoConfigruation을 가지고 작업이 진행됩니다.  

@Condition, @Conditional 은 Sprig 4.0부터 추가된 annotation이고 Spring Boot auto configuration 과정에서 사용되는 또 다른 annotation들도 [autoconfigure-condition](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/)에서 확인 가능합니다.  

또한 [@Profile](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Profile.html), [@Lazy](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Lazy.html)와 같은 Spring에서 제공하는 다른 annotation들도 Spring Boot auto configuration에 활용됩니다.  

Auto Configuration Import Filters와 몇가지 @Conditional을 살펴보는 과정을 통해 AutoConfiguration의 원리를 살펴보도록 하겠습니다.  


## Auto Configuration Import Filters

Spring Boot는 [spring.factories](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/resources/META-INF/spring.factories) 정보를 가지고 auto configruation을 진행합니다.  

그리고 그 내용 중에 AutoConfigurationImportFilter 관련 설정이 있으며 아래와 같은 3개의 필터가 적용 된 것을 확인할 수 있습니다.  

```spring.factories```

~~~properties
...(생략)
# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
org.springframework.boot.autoconfigure.condition.OnClassCondition,\
org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition

...(생략)
~~~

해당 필터들은 각 AutoConfiguration이 가진 @Conditional을 가지고 조건 만족여부를 체크 합니다. 그리고 조건이 맞지 않을 경우 해당 AutoConfiguration이 동작하지 않도록 제외 시키는 역할을 수행합니다.  

  * [org.springframework.boot.autoconfigure.condition.OnBeanCondition](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/condition/OnBeanCondition.java)
  : 특정 bean들의 존재유무에 대해서 다루는 필터입니다. 
  : 대상 : @ConditionalOnBean, @ConditionalOnMissingBean, @ConditionalOnSingleCandidate

  * [org.springframework.boot.autoconfigure.condition.OnClassCondition](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/condition/OnClassCondition.java)
  : 특정 class들의 존재유무에 대해서 다루는 필터입니다. 
  : 대상 : @ConditionalOnClass, @ConditionalOnMissingClass

  * [org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/condition/OnWebApplicationCondition.java)
  : WebApplicationContext의 존재유무에 대해서 다루는 필터입니다. 
  : 대상 : @ConditionalOnWebApplication, @ConditionalOnNotWebApplication


## @ConditionalOnMissingBean

[@ConditionalOnMissingBean](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/ConditionalOnMissingBean.html)은 특정 bean이 사전에 생성되지 않은 경우 조건이 만족됩니다. @Bean과 함께 사용된다면 이미 생성된 bean이 없을 때 해당 bean을 생성한다는 의미로 보시면 됩니다.  

우리가 특정 bean을 생성하도록 설정해놨다면, 일반적으로 AutoConfiguration의 bean생성 순서가 마지막에 오도록 AutoConfiguration이 잘 짜여져있기 때문에 우리가 설정한 bean이 먼저 생성되고 해당 AutoConfiguration은 필터링 되어 중복생성되는 상황을 막습니다. 우리가 해당 bean을 설정하지 않았다면 AutoConfiguration에서는 해당 bean을 자동 생성하게 됩니다.  

ThreadPoolTaskExecutor bean을 생성하는 [TaskExecutionAutoConfiguration.java](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/task/TaskExecutionAutoConfiguration.java)를 예로 들어보겠습니다.  

```TaskExecutionAutoConfiguration.java```

~~~java

...(생략)
@Configuration(proxyBeanMethods = false)
public class TaskExecutionAutoConfiguration {

    /**
     * Bean name of the application {@link TaskExecutor}.
     */
    public static final String APPLICATION_TASK_EXECUTOR_BEAN_NAME = "applicationTaskExecutor";

    ...(생략)

    @Lazy
    @Bean(name = { APPLICATION_TASK_EXECUTOR_BEAN_NAME,
        AsyncAnnotationBeanPostProcessor.DEFAULT_TASK_EXECUTOR_BEAN_NAME })
    @ConditionalOnMissingBean(Executor.class)
    public ThreadPoolTaskExecutor applicationTaskExecutor(TaskExecutorBuilder builder) {
        return builder.build();
    }

}
~~~

@Lazy가 걸려있기 때문에 Spring Boot 기동시에 생성되지 않고 ThreadPoolTaskExecutor가 필요한 상황에서 bean이 생성이 요청됩니다. Executor.class와 같은 class type인 bean이 이미 생성되지 않은 경우에 @ConditionalOnMissingBean 조건이 만족되고 bean생성이 진행됩니다. 즉, 우리가 아래와 같은 Executor bean을 생성하는 설정을 해뒀다면 우리가 설정한 bean이 생성되고 TaskExecutionAutoConfiguration 에 의해서는 bean생성이 이뤄지지 않습니다. 반대로 우리가 Executor bean 등록을 설정하지 않았더라도 필요한 상황이되면 해당 bean이 생성되게 됩니다.  

```CustomizedAsyncConfig.java```

~~~java
...(생략)

@Configuration
public class CustomizedAsyncConfig {

    @Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(10);
        taskExecutor.setThreadNamePrefix("Executor-");
        taskExecutor.initialize();
        return taskExecutor;
    }
}
~~~


## @ConditionalOnBean

[@ConditionalOnBean](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/ConditionalOnBean.html)은 특정 bean이 이미 생성되어있는 경우에만 조건이 만족됩니다. 작업을 위해 필수적으로 필요한 bean이 미리 생성되어있는지 체크할 때 사용할 수 있습니다.  

예를들어, JdbcTemplate를 생성하기 위해서는 DataSource가 필요합니다. 아래의 JdbcTemplate bean 생성 설정은 @ConditionalOnBean이 함께 사용되어 dataSource라고 정의된 bean이 존재할 때만 JdbcTemplate bean을 생성합니다. 만약에 dataSource가 존재하지 않는다면 JdbcTemplate을 만들 수도 없을 뿐더러 만들 필요가 없기 때문에 auto configuration 과정에서 JdbcTemplate을 bean 생성을 진행하지 않습니다.  

~~~java
    @Bean 
    @ConditionalOnBean(name={"dataSource"}) 
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        returnnew JdbcTemplate(dataSource); 
    }
~~~

참고로, 실제 [JdbcTemplateAutoConfiguration.java](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jdbc/JdbcTemplateAutoConfiguration.java)은 @ConditionalOnBean을 사용하지는 않습니다.  

<!--ads-->

## @ConditionalOnClass

[@ConditionalOnClass](https://docs.spring.io/spring-boot/docs/current/api/org/springframework/boot/autoconfigure/condition/ConditionalOnClass.html)은 classpath에 특정 class가 존재할 때만 조건이 만족됩니다. 작업을 위해 필수적으로 필요한 의존성이 등록되어있는지 체크할 때 사용할 수 있습니다.  

[H2ConsoleAutoConfiguration.java](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/h2/H2ConsoleAutoConfiguration.java)을 예로들어 좀 더 자세히 살펴보겠습니다. 브라우저에서 접근 가능한 H2 DB 콘솔 화면을 자동으로 구성하는 내용입니다.  

```H2ConsoleAutoConfiguration.java```

~~~
package org.springframework.boot.autoconfigure.h2;

...(생략)

@Configuration(proxyBeanMethods = false)
@ConditionalOnWebApplication(type = Type.SERVLET)
@ConditionalOnClass(WebServlet.class)
@ConditionalOnProperty(prefix = "spring.h2.console", name = "enabled", havingValue = "true", matchIfMissing = false)
@AutoConfigureAfter(DataSourceAutoConfiguration.class)
@EnableConfigurationProperties(H2ConsoleProperties.class)
public class H2ConsoleAutoConfiguration {

    private static final Log logger = LogFactory.getLog(H2ConsoleAutoConfiguration.class);

    @Bean
    public ServletRegistrationBean<WebServlet> h2Console(H2ConsoleProperties properties, ObjectProvider<DataSource> dataSource) {

        ...(생략)

        String path = properties.getPath();
        String urlMapping = path + (path.endsWith("/") ? "*" : "/*");
        ServletRegistrationBean<WebServlet> registration = new ServletRegistrationBean<>(new WebServlet(), urlMapping);

        ...(생략)

        return registration;
    }

}
~~~

WebServlet.java 파일이 classpath에 존재해야지만 @ConditionalOnClass의 조건이 만족됩니다. 결국 WebServlet.java를 가진 spring-boot-stater-web 과 같은 의존성이 추가되어있는 상황에서만 H2ConsoleAutoConfiguration은 동작하게 됩니다.  

그 외 다른 조건들의 의미는 다음과 같습니다.  

  * @ConditionalOnWebApplication : servlet 타입의 web application 일 경우
  * @ConditionalOnProperty : 프로퍼티에 spring.h2.console.enabled=true가 있는 경우 
  * @AutoConfigureAfter : DataSourceAutoConfiguration 이 먼저 진행 된 후에 처리 됩니다.  
  * @EnableConfigurationProperties : [H2ConsoleProperties](https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/h2/H2ConsoleProperties.java)를 이용해서 관련 프로퍼티 정보를 읽어옵니다.  

위의 조건들 모두가 만족된다면 ServletRegistrationBean<WebServlet> bean이 생성되고 브라우저로 /h2-console 에 접근하면 console을 사용할 수 있게 됩니다. 그리고 이 모든 과정은 auto configuration에 의해서 진행되고 아래와 같은 H2에 대한 의존성 주입과 프로퍼티 설정만으로도 H2 web console을 사용할 수 있게 된 것입니다.  

```pom.xml```

~~~xml
    ...(생략)
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
    </dependency>
    ...(생략)
~~~

```application.properties```

~~~properties
...(생략)
spring.h2.console.enabled=true
~~~

<!--ads-->

# Appendices 

## Disabling Specific Auto-configuration Classes

만약 특정 AutoConfiguration을 사용하지 않으려고 한다면 아래와 같이 exclude 설정을 하면 됩니다.  

~~~java
import org.springframework.boot.autoconfigure.*;
import org.springframework.boot.autoconfigure.jdbc.*;
import org.springframework.context.annotation.*;

@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
    ...(생략)
}
~~~

#### References 

  * [using-boot-disabling-specific-auto-configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html#using-boot-disabling-specific-auto-configuration)


## @SpringBootApplication

@SpringBootApplication는 @ComponentScan과 @EnableAutoConfiguration을 포함하고 있습니다.  

아래의 Application.java는 @SpringBootApplication 설정만 했지만 component scan과 auto configuration이 이뤄집니다. 

```Application.java```

~~~java
package com.dveamer.sample

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
~~~

즉, 우리는 프로젝트의 최상단에 Application.java를 위치시키고 @SpringBootApplication 설정해두면 앞으로 프로젝트에 추가할 configuration 관련 정보들이 모두 유효하도록 설정됩니다.  

