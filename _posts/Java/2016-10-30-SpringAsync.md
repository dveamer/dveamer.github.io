---
layout: post
title:  "Spring @Async 비동기처리"
date:   2016-10-30 12:00:00 
lastmod: 2018-02-06 00:00:00 
categories: Java
tags: Spring Asynchronous Multi-Thread 
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png)  
( 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 Spring을 사용하면 asynchronous, multi-thread 개발이 얼마나 편해지는지를 설명합니다.  

<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

최종 결과물에 대한 [샘플](https://github.com/dveamer/SpringBootSample/tree/master/Async)을 Github에 올려뒀으니 참고하시기 바랍니다.  

# Java 비동기방식 처리  

메시지를 저장하는 내용의 method가 있습니다.

~~~java
public class GreetingService {

    public void method1(String message) throws Exception {
        // do something
    }

}
~~~

가장 소스를 조금 변경해서 method를 비동기방식으로 처리해봅시다.  
method가 실행되면 새로운 thread를 만들고 그 thread에서 메시지를 저장하도록 처리하면 됩니다.  

~~~java
public class GreetingService {

    public void method1(final String message) throws Exception {
        new Thread(new Runnable() {
            @Override
            public void run() {
                // do something
            }            
        }).start();
    }

}
~~~

물론 이 방법은 thread를 관리할 수 없기 때문에 위험한 방법입니다.  
동시에 1000개의 호출이 이뤄진다면 동시에 1000개의 thread가 생성되는 방식입니다.  
Thread를 관리하기 위해서는 JDK 1.5부터 제공하는 java.util.concurrent.ExecutorService를 사용하면 됩니다.  

~~~java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class GreetingService {

    ExecutorService executorService = Executors.newFixedThreadPool(10);

    public void method1(final String message) throws Exception {
        executorService.submit(new Runnable() {
            @Override
            public void run() {
                // do something
            }            
        });
    }

}
~~~

하지만 ExecutorService를 사용하더라도 method를 수정해야합니다.  
그리고 비동기방식으로 처리하고 싶은 method마다 동일한 작업들을 진행해야할 것입니다.  

비동기방식으로 처리하고 싶은 method마다 반복적으로 수정작업을 해야한다는 점을 기억해주시기 바랍니다.  

# Spring @Async

## @Async with SimpleAsyncTaskExecutor

Spring의 @Async annotation을 이용하면 간단하게 처리가 가능합니다.  
Application 클래스에 @EnableAsync를 설정하고  
메소드 위에 @Async annotation만 추가하면 method는 비동기방식으로 처리됩니다.  

~~~java
@EnableAsync
@SpringBootApplication
public class Application {
    ...
}
~~~

~~~java
public class GreetingService {

    @Async
    public void method1(String message) throws Exception {
        // do something
    }

}
~~~

하지만 이 방법도 thread관리는 이 방법도 이뤄지지 않습니다.  
@Async의 기본설정은 SimpleAsyncTaskExecutor를 사용하도록 되어있기 때문입니다.  

## @Async with ThreadPoolTaskExecutor

Thread pool을 이용해서 thread를 관리가능한 방식입니다.  
아래의 SpringAsyncConfig 클래스를 추가해주시기 바랍니다.  

그리고 앞서 설정한 Application 클래스의 @EnableAsync을 제거해주시기 바랍니다.  
SpringAsyncConfig 클래스에 설정해뒀기 때문에 중복됩니다.  

Application 클래스에 @EnableAutoConfiguration(혹은 @SpringBootApplication) 설정이 되어있다면  
런타임시 @Configuration가 설정된 SpringAsyncConfig 클래스의 threadPoolTaskExecutor bean 정보를 읽어들입니다.  

~~~java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.task.AsyncTaskExecutor;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Executor;

@Configuration
@EnableAsync
public class SpringAsyncConfig {

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

기존의 Biz 클래스에 설정한 @Async annotation에 bean의 이름을 제공하면  
SimpleAsyncTaskExecutor가 아닌 설정한 TaskExecutor로 thread를 관리하게 됩니다.  

~~~java
public class GreetingService {
	
    @Async("threadPoolTaskExecutor")
    public void method1() throws Exception {
        // do something
    }

}
~~~

Thread Pool의 종류를 여러개 설정하고자한다면 SpringAsyncConfig 안에 bean을 여러개 만드시고  
@Async를 설정시 원하는 bean의 이름을 설정하시면 됩니다.  

## @Async 의 장점

좀 더 설명할 것이 남아있지만 일단 여기서 장점을 짚고 넘어가겠습니다.  

method1에 대한 수정없이 처리가 가능하다는 점이 장점입니다.  
기존에는 동기/비동기에 따라서 method1의 내용이 달라집니다.  
개발할 때 동기/비동기에 대한 고민없이 개발자는 메시지를 저장하는 과정에만 집중하면 됩니다.  
비동기로 처리해야되면 @Async annotation만 붙여주면 되니까요.  

물론 Biz객체는 client객체에게 DI로 제공되어야만 합니다.  
우리는 DI(Dependance Injection)만 신경쓰면 부수적인 것은 Spring이 다해줍니다.  

## 제약사항 

  * pulbic method에만 사용가능 합니다.  
  * 같은 객체내의 method끼리 호출시 async method는 동작하지 않습니다.  

[Spring @Async AspectJ 비동기처리](/java/SpringAsyncAspectJ.html) 글에서 AspectJ를 이용해서 제약사항을 회피하는 방법을 설명드립니다.  

## @Async with Logging Exception

비동기로 처리되는 method에서 exception이 발생하면 어떻게 될까요?  
해당 thread만 죽습니다.  
전체 프로세스에는 영향이 없지만 해당 thread가 소리없이 죽기 때문에 관리가 되지 않습니다.  
이번에는 SpringAsyncConfig 를 수정해서 logging 처리까지 해보도록 하겠습니다.  

Github에 올려둔 [샘플](https://github.com/dveamer/SpringBootSample/tree/master/Async)을 참고하시기 바랍니다.  

~~~java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.task.AsyncTaskExecutor;
import org.springframework.scheduling.annotation.EnableAsync;
import org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor;

import java.util.concurrent.Callable;
import java.util.concurrent.Executor;
import java.util.concurrent.Future;

@Configuration
@EnableAsync
public class SpringAsyncConfig {

    protected Logger logger = LoggerFactory.getLogger(getClass());
    protected Logger errorLogger = LoggerFactory.getLogger("error");

    @Bean(name = "threadPoolTaskExecutor")
    public Executor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(3);
        taskExecutor.setMaxPoolSize(30);
        taskExecutor.setQueueCapacity(10);
        taskExecutor.setThreadNamePrefix("Executor-");
        taskExecutor.initialize();
        return new HandlingExecutor(taskExecutor); // HandlingExecutor로 wrapping 합니다.
    }

    public class HandlingExecutor implements AsyncTaskExecutor {
        private AsyncTaskExecutor executor;

        public HandlingExecutor(AsyncTaskExecutor executor) {
            this.executor = executor;
        }

        @Override
        public void execute(Runnable task) {
            executor.execute(task);
        }

        @Override
        public void execute(Runnable task, long startTimeout) {
            executor.execute(createWrappedRunnable(task), startTimeout);
        }

        @Override
        public Future<?> submit(Runnable task) {
            return executor.submit(createWrappedRunnable(task));
        }

        @Override
        public <T> Future<T> submit(final Callable<T> task) {
            return executor.submit(createCallable(task));
        }

        private <T> Callable<T> createCallable(final Callable<T> task) {
            return new Callable<T>() {
                @Override
                public T call() throws Exception {
                    try {
                        return task.call();
                    } catch (Exception ex) {
                        handle(ex);
                        throw ex;
                    }
                }
            };
        }

        private Runnable createWrappedRunnable(final Runnable task) {
            return new Runnable() {
                @Override
                public void run() {
                    try {
                        task.run();
                    } catch (Exception ex) {
                        handle(ex);
                    }
                }
            };
        }

        private void handle(Exception ex) {
            errorLogger.info("Failed to execute task. : {}", ex.getMessage());
            errorLogger.error("Failed to execute task. ",ex);
        }

    }

}
~~~

## Return Type

비동기 방식에서 void가 아닌 다른 return type은 Future로 전달합니다.  

~~~java
public class GreetingService {

    @Async("threadPoolTaskExecutor")
    public Future<String> method1(String message) throws Exception {
        // do something
        return new AsyncResult<String>("Success");
    }
}
~~~

method1을 호출한 client에서는 Future의 isDone, get 등의 method를 이용해서 return 값을 사용할 수 있습니다.  

#### References
  * [Future](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html)
  * [스프링에서 @Async로 비동기처리하기](http://springboot.tistory.com/38)
  * [Spring Async Uncaught Exception handler](http://stackoverflow.com/questions/8735870/spring-async-uncaught-exception-handler)

