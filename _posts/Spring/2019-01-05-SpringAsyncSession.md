---
layout: post
title:  "Spring @Async with Http Session"
date:   2019-01-05 12:00:00 
lastmod: 2019-01-05 12:00:00  
categories: BackEnd
tags: Spring Asynchronous Session Multi-Thread 
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 [Spring @Async 비동기처리](/java/SpringAsync.html) 에서 알려드린 Spring @Async의 제약사항 중 thread 분리에 의해 HttpServletRequest에 접근 못하는 상황을 다시 한번 살펴보고 해결방법에 대해서 알려드립니다.  
 
<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

[Spring @Async Session 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncSession)을 Github에 올려뒀으니 참고하시기 바랍니다.  
위의 샘플은 [Spring @Async AspectJ - CTW 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncCTW)을 기반으로 작성하되 Spring Boot 버전을 높였습니다. Gradle 4.x 버전으로 실행해야 합니다.  

# @Async with Http Session

## 문제 제기 

Spring @Async를 쓰면 업무로직 변화없이 쉽게 비동기 처리를 진행 할 수 있습니다. 하지만 업무로직에서 HttpSession을 이용하거나 HttpServletRequest를 사용한다면 문제가 발생합니다. 물론 HttpSession에서 필요한 값들만 모아서 VO(Value Object)로 service로 넘긴다면 큰 문제가 되지 않겠지만 우리는 필요에 의해 controller, service, DAO 전역에서 HttpServletRequest에 직접 접근하는 방법을 사용하곤 합니다.  

왜 그런 필요가 생기는지와 어떻게 직접 접근하는지에 대해서는 [Spring RequestContextHolder - 어디서든 HttpServletReqeust 사용하기](/BackEnd/SpringRequestContextHolder) 글을 참고해주시기 바랍니다.  

~~~java
@Service
public class GreetingService {

    Logger logger = LoggerFactory.getLogger(getClass());

    public void test() {

        String loggingCode = UUID.randomUUID().toString().replaceAll("-", "");

        logger.info("loggingCode {} is generated", loggingCode);

        Attribute attribute = new Attribute();
        attribute.setLoggingCode(loggingCode);

        SessionScopeUtil.setAttribute(attribute);

        logger.info("loggingCode {} is stored on Session Scope", loggingCode);

        method1();

        method2();
    }

    @Async("threadPoolTaskExecutor")
    public void method1() {
        try{
            Attribute attribute = SessionScopeUtil.getAttribute();
            String loggingCode = attribute.getLoggingCode();

            logger.info("method1() > Successfully received loggingCode {}", loggingCode);
        }catch(Exception ex){
            logger.info("method1() > Failed to receive loggingCode");
        }
    }

    @Async("executorWithSession")
    public void method2() {
        try{
            Attribute attribute = SessionScopeUtil.getAttribute();
            String loggingCode = attribute.getLoggingCode();

            logger.info("method2() > Successfully received loggingCode {}", loggingCode);
        }catch(Exception ex){
            logger.info("method2() > Failed to receive loggingCode");
        }
    }

}
~~~

test() 에서 loggingCode를 만들고 SessionScopeUtil에 보관 후 method1()과 method2()를 호출 합니다.  
method1(), method2() 에서는 SessionScopeUtil에서 loggingCode를 꺼내서 로그에 남깁니다.  

SesssionScopeUtil에 대해서는 [Spring RequestContextHolder - 어디서든 HttpServletReqeust 사용하기](/BackEnd/SpringRequestContextHolder) 글을 참고해주시기 바랍니다. 간단히 설명드리면 SesssionScopeUtil에 보관한 attribute는 HttpServletRequest.setSession()에 보관되는 것과 동일하다고 보시면 됩니다.  
즉, 다른 WAS에서도 동일한 SESSION_ID를 가진 호출을 받는다면 SessionScopeUtil을 통해서 동일한 attribute를 사용할 수 있습니다.  

만약 mehtod1(), method2()가 모두 비동기가 아닌 동기방식으로 실행되었다면 당연히 모두 성공적으로 loggingCode를 로그에 남길 수 있었을 겁니다.  
test()에서 session scope에 기록을 했고 같은 process, 같은 thread인 method1()에서 당연히 session scope에서 loggingCode를 꺼낼 수 있습니다.  

어떠한 이유로 method1()과 method2()를 비동기방식으로 변경해야된다면 [Spring @Async 비동기처리](/java/SpringAsync.html) 글에서는 얘기한대로 업무로직 변경없이 @Async만 추가하면 될까요?  
만약 method1()과 method2()가 매우 복잡한 업무로직을 가지고 있다면 @Async를 마음 놓고 추가할 수 있을까요?  
예를들어, 위와 같이 HttpServletRequest의 Session을 활용하는 업무로직을 비동기 처리하면 정상적인 결과를 얻지 못합니다.  

하지만 SpringAsyncConfig 설정 과정에서 몇가지 작업을 통해 마음놓고 @Async를 적용할 수 있는 해결방법을 알려드립니다.  
method1()를 비동기 처리하는 threadPoolTaskExecutor은 그냥 기본적인 Spring @Async으로 설정해서 처리실패하도록 하고 method2()를 비동기 처리하는 executorWithSession은 Session을 활용할 수 있도록 설정하겠습니다.  

## Result

SpringAsyncConfig 설정방법을 보여드리기 전에 테스트 결과를 먼저 설명드립니다.  

~~~
2019-01-05 22:25:06,606 INFO i.d.s.h.GreetingService [http-nio-8080-exec-1] loggingCode 43815bedf5dc452ca1a8ea5b8a9aa256 is generated
2019-01-05 22:25:06,668 INFO i.d.s.h.GreetingService [http-nio-8080-exec-1] loggingCode 43815bedf5dc452ca1a8ea5b8a9aa256 is stored on Session Scope
2019-01-05 22:25:06,689 INFO i.d.s.h.GreetingService [Executor-1] method1() > Failed to receive loggingCode
2019-01-05 22:25:06,710 INFO i.d.s.h.GreetingService [ExecutorWithSession-1] method2() > Successfully received loggingCode 43815bedf5dc452ca1a8ea5b8a9aa256
~~~

test()는 http-nio-8080-exec-1 thread에서 실행됐고 method1()은 Executor-1 thread에서 실행되고 실패했습니다.  
그리고 method2()는 ExecutorWithSession-1 thread에서 실행되고 loggingCode를 가져오는데 성공했습니다.  

앞서 얘기 드린대로 추가기능이 설정된 executorWithSession은 SessionScopeUtil을 통해 loggingCode를 전달해주는 것이 확인됩니다.  

## SpringAsyncConfig 

~~~java
...(생략)

@Configuration
@EnableAsync(mode=AdviceMode.ASPECTJ)
public class SpringAsyncConfig {

    ...(생략)

    @Bean(name = "threadPoolTaskExecutor", destroyMethod = "shutdown")
    public Executor threadPoolTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        ...(생략)

        return taskExecutor;
    }

    @Bean(name = "executorWithSession", destroyMethod = "destroy")
    public Executor executorWithSession() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        ...(생략)

        return new HandlingExecutor(taskExecutor); // HandlingExecutor로 wrapping 합니다.
    }

    public class HandlingExecutor implements AsyncTaskExecutor {

        private AsyncTaskExecutor executor;

        public HandlingExecutor(AsyncTaskExecutor executor) {
            this.executor = executor;
        }

        ...(생략)

        @Override
        public void execute(Runnable task) {
            Runnable runnable = createWrappedRunnable(task);
            executor.execute(runnable);
        }

        ...(생략)

        private Runnable createWrappedRunnable(final Runnable task) {
            Attribute attr = SessionScopeUtil.getAttribute();

            return new AttributeAwareTask(new Runnable() {
                @Override
                public void run() {
                    try {
                        task.run();
                    } catch (Exception ex) {
                        handle(ex);
                    }
                }
            }, attr);
        }

        ...(생략)
    }

    public class AttributeAwareTask<T> implements Callable<T>, Runnable {
        private Callable<T> callable;
        private Runnable runnable;
        private Attribute attribute;

        ...(생략)

        public AttributeAwareTask(Runnable task, Attribute attribute) {
            this.runnable = task;
            this.attribute = attribute;
        }

        ...(생략)

        @Override
        public void run() {
            AsyncScopeUtil.setAttribute(attribute);
            try {
                runnable.run();
            } finally {
                AsyncScopeUtil.removeAttribute();
            }
        }
    }


}
~~~

[Spring @Async 비동기처리](/java/SpringAsync.html) 글에서 설명드린 handler는 exception 처리를 위해서만 사용됐습니다.  
지금은 handler를 약간 수정해서 createWrappedRunnable() 에서 runnable AttributeAwareTask로 wrapping합니다. 그리고 wrapping하는 과정에서 SessionScopeUtil에서 꺼낸 attribute를 전달합니다.  

앞서 테스트 결과를 통해 설명하자면, GreetingService.test()는 http-nio-8080-exec-1 thread에서 실행됐고 GreetingService.method2()를 호출 했습니다.  
호출 후 비동기로 실행 되기 직전까지의 과정을 잠시 살펴보겠습니다.  

GreetingService.method2()를 실행하라는 내용을 가진 runnable이 생성되어 HandlingExecutor.execute() 로 전달됩니다.  
위의 코드에 포함된 내용이지만 구간별로 나눠서 설명을 하기 위해 HandlingExecutor.execute()의 내용을 한번 더 기술하겠습니다.  

~~~java
    public class HandlingExecutor implements AsyncTaskExecutor {

        private AsyncTaskExecutor executor;

        ...(생략)

        @Override
        public void execute(Runnable task) {
            Runnable runnable = createWrappedRunnable(task);
            executor.execute(runnable);
        }
        ...(생략)

    }
~~~

전달된 runnable은 createWrappedRunnable()를 통해 AttributeAwareTask로 wrapping 된 후 AsyncTaskExecutor.execute()에 의해 비동기로 실행됩니다.  
비동기로 실행되기 전에 wrapping이 이뤄졌음을 주의해주시기 바랍니다. 그러면 이제 아직 비동기로 실행되기 전의 wrapping 과정을 살펴보겠습니다.  

~~~java
    public class HandlingExecutor implements AsyncTaskExecutor {

        ...(생략)

        private Runnable createWrappedRunnable(final Runnable task) {
            Attribute attr = SessionScopeUtil.getAttribute();

            return new AttributeAwareTask(new Runnable() {
                @Override
                public void run() {
                    try {
                        task.run();
                    } catch (Exception ex) {
                        handle(ex);
                    }
                }
            }, attr);
        }
        ...(생략)

    }
~~~

SessionScopeUtil로부터 attribute를 받고 runnable과 함께 AttributeAwareTask로 wrapping합니다.  
이 때는 비동기로 실행되기 전이기 때문에 SessionScopeUtil로부터 받은 attribute는 http-nio-8080-exec-1 thread의 HttpServletRequest로부터 받은 attribute 입니다.  

이제 다시 AsyncTaskExecutor.execute()가 호출되고 비동기로 실행된 직후를 살펴보겠습니다.  

~~~java
    public class AttributeAwareTask<T> implements Callable<T>, Runnable {
        private Callable<T> callable;
        private Runnable runnable;
        private Attribute attribute;

        ...(생략)

        @Override
        public void run() {
            AsyncScopeUtil.setAttribute(attribute);
            try {
                runnable.run();
            } finally {
                AsyncScopeUtil.removeAttribute();
            }
        }
    }
~~~

비동기로 실행되었으니 현재는 ExecutorWithSession-1 thread로 무대가 바꼈습니다. 새로운 무대에서 아까 전달받은 attribute를 AsyncScopeUtil에 보관합니다.  
그 결과, method2()에서 SessionSopeUtil의 트릭을 통해 attribute를 꺼낼 수 있게 됩니다.  

그 작업이 끝나면 runnable이 실행되고 그 후 AsyncScopeUtil에서 아까 보관한 attribute를 제거합니다.  
만약 제거 과정을 빠뜨리시면 memory leak이 발생되니 절대 빼시면 안됩니다.  

## SessionScopeUtil & AsyncScopeUtil

위의 설정만으로는 thread가 분리되었음에도 Session에 보관된 attribute를 꺼내쓰는 내용이 설명되지 않습니다.  
SessionScopeUtil & AsyncScopeUtil 이 가진 간단한 트릭을 설명드립니다.  

### AsyncScopeUtil

AsyncScopeUtil은 static으로 선언된 Map에 thread ID를 key로하여 자료를 관리합니다.  
동일한 thread 내에서는 Controller-Service-Dao 중 어느 곳에서든지 attribute를 보관하고 꺼내서 사용할 수 있습니다.  

~~~java
public class AsyncScopeUtil {

    private static final Map<String, Attribute> attributeMap = new HashMap<>();

    public static Attribute getAttribute() {
        return attributeMap.get(Long.toString(Thread.currentThread().getId()));
    }

    public static void setAttribute(Attribute attribute) {
        attributeMap.put(Long.toString(Thread.currentThread().getId()), attribute);
    }

    public static void removeAttribute() {
        attributeMap.remove(Long.toString(Thread.currentThread().getId()));
    }
}
~~~

### SessionScopeUtil

~~~java
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

public class SessionScopeUtil {

    public static Attribute getAttribute() {
        try{
            return (Attribute) RequestContextHolder.getRequestAttributes().getAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
        }catch(Exception ex){
            return AsyncScopeUtil.getAttribute();
        }
    }

    public static void setAttribute(Attribute attribute) {
        RequestContextHolder.getRequestAttributes().setAttribute(Attribute.KEY, attribute, RequestAttributes.SCOPE_SESSION);
    }

    public static void removeAttribute() {
        RequestContextHolder.getRequestAttributes().removeAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
    }
}
~~~

```SessionScopeUtil.getAttribute()``` 에는 한가지 트릭이 있습니다.  
ThreadPoolTaskExecutor이 가진 thread 들은 HttpServletRequest를 가지고 있지 않기 때문에 exception이 발생하고 ```AsyncScopeUtil.getAttribute()``` 가 호출됩니다.  
그 결과, 동일한 Session ID를 갖는 WAS간의 자료 공유를 이루거나 동일한 thread간의 자료 공유를 이뤄줍니다.  


## 주의사항 

위와 같이한다고 모든 것이 다 해결되는 것은 아닙니다.  

동기든 비동기든 업무로직에서 SessionScopeUtil의 getAttribute()만 사용하는 것을 권유 드립니다.  
비동기로 처리될 때 setAttribute() 혹은 removeAttribute()를 실행하면 에러가 발생합니다.  

두번째 주의사항은 Controller가 실행된 thread가 @Async의 thread 보다 먼저 종료 될 수 있다는 점입니다.  
저의 경우에는 비동기로 처리할 thread에게 attribute라는 VO를 전달했습니다. 근데 소스를 조금만 수정하면 controller가 가지고 있던 context 혹은 HttpServletRequest를 직접 전달 할 수 있습니다. 하지만 전달 된 것을 사용하는 과정에서 controller가 종료되면 해당 context 혹은 request가 종료되었다는 에러 메시지를 만나게 되실 겁니다.  


