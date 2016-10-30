---
layout: post
title:  "Spring @Async 비동기처리"
date:   2016-10-30 12:00:00 
categories: Java
tags: Asynchronous Multi-Thread 
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png)  
( 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 Spring을 사용하면 asynchronous, multi-thread 개발이 얼마나 편해지는지를 설명합니다.  

<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

# Java 비동기방식 처리  

메시지를 저장하는 내용의 method가 있습니다.

~~~java
public class Biz {

    public void save(String message) throws Exception {
        // save message
    }

}
~~~

가장 소스를 조금 변경해서 save method를 비동기방식으로 처리해봅시다.  
save method가 실행되면 새로운 thread를 만들고 그 thread에서 메시지를 저장하도록 처리하면 됩니다.  

~~~java
public class Biz {

    public void save(String message) throws Exception {
        new Thread(new Runnable() {
            @Override
            public void run() {
                // save message
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

public class Biz {

    ExecutorService executorService = Executors.newFixedThreadPool(10);

    public void save(String message) throws Exception {
        executorService.submit(new Runnable() {
            @Override
            public void run() {
                // save message
            }            
        });
    }

}
~~~

하지만 ExecutorService를 사용하더라도 save method를 수정해야합니다.  
그리고 비동기방식으로 처리하고 싶은 method마다 동일한 작업들을 진행해야할 것입니다.  

비동기방식으로 처리하고 싶은 method마다 반복적으로 수정작업을 해야한다는 점을 기억해주시기 바랍니다.  

# Spring @Async

## @Async with SimpleAsyncTaskExecutor

Spring의 @Async annotation을 이용하면 간단하게 처리가 가능합니다.  
메소드 위에 @Async annotation만 추가하면 save method는 비동기방식으로 처리됩니다.  

~~~java
public class Biz {

    @Async
    public void save(String message) throws Exception {
        // save message
    }

}
~~~

하지만 이 방법도 thread관리는 이 방법도 이뤄지지 않습니다.  
@Async의 기본설정은 SimpleAsyncTaskExecutor를 사용하도록 되어있기 때문입니다.  

## @Async with ThreadPoolTaskExecutor

Thread pool을 이용해서 thread를 관리가능한 방식입니다.  
아래의SpringAsyncConfig 클래스를 추가해주시기 바랍니다.  

Application 클래스에 @EnableAutoConfiguration 설정이 되어있다면  
@Configuration 설정을 한 SpringAsyncConfig 클래스를 추가하면 런타임시 threadPoolTaskExecutor bean 정보를 읽어들입니다.  

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
public class Biz {
	
    @Async("threadPoolTaskExecutor")
    public void save(String message) throws Exception {
        // save message
    }

}
~~~

Thread Pool의 종류를 여러개 설정하고자한다면 SpringAsyncConfig 안에 bean을 여러개 만드시고  
@Async를 설정시 원하는 bean의 이름을 설정하시면 됩니다.  

## @Async 의 장점

좀 더 설명할 것이 남아있지만 일단 여기서 장점을 짚고 넘어가겠습니다.  

save method에 대한 수정없이 처리가 가능하다는 점이 장점입니다.  
기존에는 동기/비동기에 따라서 save method의 내용이 달라집니다.  
개발할 때 동기/비동기에 대한 고민없이 개발자는 메시지를 저장하는 과정에만 집중하면 됩니다.  
비동기로 처리해야되면 @Async annotation만 붙여주면 되니까요.  

물론 Biz객체는 client객체에게 DI로 제공되어야만 합니다.  
우리는 DI(Dependance Injection)만 신경쓰면 부수적인 것은 Spring이 다해줍니다.  

## 제약사항 
  * pulbic method에만 사용가능 합니다.  
  * 같은 객체내의 method끼리 호출시 async method는 동작하지 않습니다.  

## @Async with Logging Exception

save method에서 exception이 발생하면 어떻게 될까요?  
Process에는 영향없고 해당 thread는 죽습니다.  

Logging이 어떻게 될지는 저도 체크를 안해봐서 추측만해보자면..  
ThreadPoolTaskExecutor 에서 logging을 해뒀다 할지라도  
Log 설정을 어떻게 하느냐에 따라 log파일에 출력여부가 달라질 것입니다.  

이번에는 SpringAsyncConfig 를 수정해서 logging 처리까지 해보도록 하겠습니다.  

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

import io.dveamer.test.NotiException;

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
                        logger.info("async test");
                        return task.call();
                    } catch (NotiException ex) {
                        handle(ex);
                        throw ex;
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
                        logger.info("async test");
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

        private void handle(NotiException ex) {
            errorLogger.info("Stop a task. : {}", ex.getMessage());
        }

    }

}
~~~

## Return Type

비동기 방식에서 void가 아닌 다른 return type은 Future로 전달합니다.  

~~~java
public class Biz {

    @Async("threadPoolTaskExecutor")
    public Future<String> save(String message) throws Exception {
        // save message
        return new AsyncResult<String>("Success");
    }
}
~~~

save method를 호출한 client에서는 Future의 isDone, get 등의 method를 이용해서 return 값을 사용할 수 있습니다.  

#### References
  * [Future](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/Future.html)
  * [스프링에서 @Async로 비동기처리하기](http://springboot.tistory.com/38)
  * [Spring Async Uncaught Exception handler](http://stackoverflow.com/questions/8735870/spring-async-uncaught-exception-handler)

