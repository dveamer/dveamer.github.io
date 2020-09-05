---
layout: post
title:  "중복 로그인 방지 in Session Clustering Env"
date:   2019-01-09 12:00:00 
lastmod: 2019-01-09 12:00:00  
categories: BackEnd
tags: Spring Login
---

중복 로그인 방지 처리를 하는 방법을 알아봅니다. WAS는 다중화 되어있고 WAS의 session clustering 기능을 이용하고 있는 상황이라고 가정합니다.  

Spring Security에서 처리하는 원리를 대략적으로 설명합니다. 그리고 Spring Security를 사용하지 않는 시스템에서 처리할 방법을 설명합니다. 먼저 Spring Security가 사용하는 방법과 동일한 원리를 이용하는 방법과 HTTP 호출을 통한 또 다른 방법에 대해서 설명드립니다.  

<!--more-->

사용자가 dveamer라는 계정으로 로그인을 시도 했는데 dveamer로 로그인한 session이 이미 존재한다고 가정합니다. UX 차원에서 중복 로그인 방지를 생각해보면 아래와 같이 두 가지 정도의 케이스가 있을 겁니다.  

  * 기존 session을 유지하고 신규 로그인 시도를 거절한다.  
  * 신규 session을 로그인 처리하고 기존 session을 로그아웃 시킨다.  

이러한 UX차원의 고민은 이번 글을 주제가 아니므로 생략하도록 하겠습니다. 이번 글의 주 관심사는 session clustering 환경에서 중복 로그인 방지 방법입니다.  

# Spring Security

## 기본 설정

~~~java
...(생략)

@Configuration
@EnableWebSecurity
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.sessionManagement()
                .maximumSessions(1)
                .maxSessionsPreventsLogin(true)
                .expiredUrl("/duplicated-login")
                .sessionRegistry(sessionRegistry);
    }

    @Bean
    public SessionRegistry sessionRegistry() {
        return new SessionRegistryImpl();
    }

    ...(생략)
}
~~~

이와 같이 Spring-Security 설정으로 로그인 처리와 중복 로그인 방지 처리가 가능합니다.  

주의사항으로는 Spring Security에서 제공하는 Principal, UserDetails interface를 구현하는 객체는 equals와 hashcode를 반드시 override하셔야 중복 로그인 방지 처리가 가능해집니다.  


## HttpSessionEventPublisher

다만, 지금의 설정만으로는 WAS가 하나만 있을 때는 잘 동작하는 것으로 보이겠지만 session clustering 환경에서 로그인 방지 처리가 성공적으로 이뤄지지 않습니다. session의 추가 혹은 삭제라는 변경사항이 발생하면 모든 WAS로 전파는 되지만 Spring Security까지 전달이 되지 않습니다. Spring Security가 전달받기 위해서는 아래와 같은 리스너 등록 작업이 필요합니다.  

~~~java
...(생략)
import org.springframework.security.web.session.HttpSessionEventPublisher;

@Configuration
@EnableWebSecurity
public class SpringSecurityConfig extends WebSecurityConfigurerAdapter {

    ...(생략)

    @Bean
    public ServletListenerRegistrationBean<HttpSessionEventPublisher> httpSessionEventPublisher() {
        return new ServletListenerRegistrationBean<HttpSessionEventPublisher>(new HttpSessionEventPublisher());
    }
}
~~~

Spring Boot의 내장 tomcat을 사용하는 것이 아니라 외부의 WAS를 이용한다면 ```web.xml```에 아래와 같이 리스너 등록을 해주셔야 합니다.  


~~~xml
<listener>
    <listener-class>org.springframework.security.web.session.HttpSessionEventPublisher</listener-class>
</listener>
~~~

[HttpSessionEventPublisher 소스코드](https://github.com/spring-projects/spring-security/blob/master/web/src/main/java/org/springframework/security/web/session/HttpSessionEventPublisher.java)를 살펴보면 javax.servlet.http.HttpSessionListener의 구현체인 것을 확인할 수 있습니다.  

Session이 추가되거나 삭제되면 WAS에는 등록된 리스너를 호출해주고 이를 통해 Spring Security는 session의 변동유무를 알 수 있게 됩니다. Session이 추가되었다면 추가된 session을 보관했다가 나중에 중복 로그인 체크시에 활용하게 됩니다. [SessionRegistryImpl 소스코드](https://github.com/spring-projects/spring-security/blob/master/core/src/main/java/org/springframework/security/core/session/SessionRegistryImpl.java)를 보시면 session정보를 어떻게 보관하는지 확인할 수 있습니다.  

참고로 이러한 중복 로그인 방지처리를 Spring에서는 "Concurrent Session Controll"라고 부릅니다.  

<!--ads-->

# Without Spring Security

Spring Security를 사용하지 않는 시스템에서 중복 로그인을 처리할 방법을 간략하게 소개합니다.  
Spring은 사용하는데 Spring Security를 사용하지 않거나 프레임워크 없이 순수 Java로 구성된 시스템에서 사용할 수 있습니다.  

javax.servlet.http.HttpSessionListener 와 javax.servlet.http.HttpSessionAttributeListener의 구현체를 하나 만듭니다.  

~~~java

import io.dveamer.sample.common.scope.Attribute;
import io.dveamer.sample.models.User;

import javax.servlet.http.*;
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;

public class CustomHttpSessionListener implements HttpSessionListener, HttpSessionAttributeListener {

    private static Map<Long, HttpSession> map = Collections.synchronizedMap(new HashMap<>());

    public static boolean isAlreadyLogedIn(Long userId, String sessionId) {
        HttpSession session = map.get(userId);

        if(session.getId().equals(sessionId)){
            return true;
        }

        return false;
    }

    public static HttpSession getSession(Long userId){
        return map.get(userId);
    }

    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        HttpSession session = se.getSession();

        try{  
            Attribute attribute = (Attribute) session.getAttribute(Attribute.KEY);
            User user = attribute.getUser();

            map.remove(user.getId());
        }catch(Exception ex){
            // 로그인 된 session 은 user를 포함한 attribute 를 가짐
        }
    }

    @Override
    public void attributeAdded(HttpSessionBindingEvent se) {
        HttpSession session = se.getSession();

        try{
            Attribute attribute = (Attribute) session.getAttribute(Attribute.KEY);
            User user = attribute.getUser();

            map.put(user.getId(), session);
        }catch(Exception ex){
            // 로그인 된 session 은 user를 포함한 attribute 를 가짐
        }
    }

    @Override
    public void sessionCreated(HttpSessionEvent se) {
        // do nothing.
    }

    @Override
    public void attributeRemoved(HttpSessionBindingEvent se) {
        // do nothing.
    }

    @Override
    public void attributeReplaced(HttpSessionBindingEvent se) {
        // do nothing.
    }

}
~~~

session에 attribute까 추가 되면 map에 보관하고 session이 삭제되면 map에서 제거하는 내용을 담고 있습니다. 그리고 isAlreadyLogedIn()을 통해서 현재 로그인 요청을 받은 userId에 대해 이미 로그인된 session이 있는지 체크할 수 있고 필요하다면 그 session을 추출할 수 있습니다.  

참고로 위의 샘플은 중복로그인 방지방법의 원리를 설명드리기 위해 굉장히 심플한 상황을 가정하고 만든 예제이므로 적절히 수정하셔야 합니다. 또한 listener에서 session을 관리하는 것은 올바른 모양새가 아닙니다.  

주의사항으로는 multi-thread에서 접근하기 때문에 동시성에 대해서 잘 처리해야하고 만료된 session이 map에서 제대로 제거되지 않는다면 memory leak이 발생할 수 있기 때문에 잘 처리하셔야 합니다.  


마지막으로 해야될 작업은 위에서 만든 listener를 등록해주는 것입니다.  

~~~xml
<listener>
    <listener-class>io.dveamer.sample.CustomHttpSessionListener</listener-class>
</listener>
~~~

<!--ads-->

# WAS 재호출을 이용한 방법

javax.servlet.http.HttpSessionListener 혹은 javax.servlet.http.HttpSessionAttributeListener와 같은 listener를 등록할 필요가 없습니다. 또한 sessionId만 가지고 기존 session을 로그아웃 시키는 방법이기 때문에 로그인 된 모든 session 객체를 WAS의 메모리에 보관할 필요가 없습니다.  

[중복 로그인 방지 샘플](https://github.com/dveamer/SpringBootSample/tree/master/PreventMultipleLogin)을 참고하시기 바랍니다.  

[Spring @Async Session 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncSession) 샘플을 기반으로 작성되었고 DB사용을 위해 H2 DB와 JPA 설정이 추가 되었습니다.  


## 로그인 처리 

단순한 로그인 로직을 작성했습니다.  


~~~java

@Service
@Transactional(rollbackFor=Exception.class, propagation= Propagation.REQUIRED)
public class AuthService {

    Logger logger = LoggerFactory.getLogger(getClass());

    @Autowired
    AuthenticationRepository authenticationRepository;

    @Autowired
    UserRepository userRepository;


    ...(생략)

    public Authentication login(Authentication auth) {

        logger.debug("This user is trying to log-in : {}", auth);

        User user = userRepository.findByUserName(auth.getUser().getUserName());
        logger.debug("Found user information from DB : {}", user);
        if(user==null || !user.getPswd().equals(auth.getUser().getPswd())){
            return new Authentication();
        }

        auth.setId(user.getId());
        auth.setLoggedIn(true);

        removeDuplicatedUserSession(auth);

        authenticationRepository.save(auth);

        Attribute attr = new Attribute();
        attr.setUser(user);
        SessionScopeUtil.setAttribute(attr);

        logger.debug("Success to log-in : {}", auth);

        return auth;
    }

    ...(생략)

    private void removeDuplicatedUserSession(Authentication auth) {

        if(!authenticationRepository.existsById(auth.getId())){
            return;
        }

        Authentication prevAuth = authenticationRepository.getOne(auth.getId());

        if(prevAuth.getSessionId().equals(auth.getSessionId())){
            return;
        }

        logger.info("Found a duplicated session : {}", prevAuth);

        HttpHeaders headers = new HttpHeaders();
        headers.add("Cookie", String.format("JSESSIONID=%s; Path=/;", prevAuth.getSessionId()));

        HttpEntity<String> entity = new HttpEntity<>("", headers);

        ResponseEntity<Authentication> res = restTemplate.postForEntity(urlLogout, entity, Authentication.class);

        if(res.getStatusCode()== HttpStatus.OK && !res.getBody().isLoggedIn()){
            logger.info("Resolved. The current status of duplicated session : {}", res.getBody());
            return;
        }

        logger.info("Failed to resolve the duplicated session problem.");
    }

}
~~~

로그인 요청이 들어오면 DB를 조회해서 기존에 로그인 된 sessionId를 확인하고 해당 sessionId를 로그아웃 시킵니다. 로그 아웃 시키는 방법은 HTTP 호출을 통해서 WAS에게 logout을 요청합니다. HTTP 호출시 header에 기존 sessionId를 cookie값으로 넣기 때문에 WAS는 기존 session에 대해 logout 처리를 진행하게 됩니다.  


HTTP 호출을 할 때 바라보는 URI에 대한 application.properties 설정입니다.  

~~~properties
uri.preventDuplicatedLogin=http://localhost:8080/auth/logout
~~~

현재 샘플 코드는 localhost:8080을 바라보도록 되어있습니다. 만약 여러개의 WAS instance가 하나의 장비에서 구동되어 port 번호를 각각 다르게 가져간다면 문제가 됩니다. 프로퍼티 설정을 각 서버마다 다르게 하거나 localhost가 아닌 L4의 VIP를 호출하도록 설정해야 합니다. 다만 localhost가 아닌 경우, 네트워크를 타기 때문에 네트워크에 문제가 발생시 정확한 처리가 안될 수 있습니다.  


## 테스트 

### 최초 로그인 요청 

~~~terminal
$ curl -i -H "Content-Type: application/json" -X POST -d '{"userName":"dveamer", "pswd":"1111"}' localhost:8080/auth/login

HTTP/1.1 200 
Set-Cookie: JSESSIONID=408495394AB2809A0EAE0D60056012BC; Path=/; HttpOnly
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 09 Jan 2019 18:49:58 GMT

{"id":1,"user":{"userName":"dveamer","pswd":"****"},"loggedIn":true}$ 
~~~

```/auth/login```로 로그인 요청을 했습니다. userName(dveamer)과 pswd(1111)를 입력했고 정상적으로 로그인 처리 되었습니다. Respose HTTP header에서 JSESSIONID가 ```408495394AB2809A0EAE0D60056012BC``` 인 것이 확인됩니다.  

### 로그인 상태 확인

~~~terminal
$ curl -i -H "Content-Type: application/json" --cookie "JSESSIONID=408495394AB2809A0EAE0D60056012BC; Path=/;" -X GET localhost:8080/auth/status

HTTP/1.1 200 
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 09 Jan 2019 18:50:12 GMT

{"id":1,"user":{"id":1,"userName":"dveamer","pswd":"****"},"loggedIn":true}$ 
~~~

```408495394AB2809A0EAE0D60056012BC``` JSESSIONID의 로그인 상태를 확인해봅니다. Cookie에 JSESSIONID를 입력해서 ```/auth/status```을 호출했고 결과는 로그인 중으로 확인됩니다.  


### 또 다른 로그인 요청

~~~terminal 
$ curl -i -H "Content-Type: application/json" -X POST -d '{"userName":"dveamer", "pswd":"1111"}' localhost:8080/auth/login
HTTP/1.1 200 
Set-Cookie: JSESSIONID=FA11A29DC084CCB0111BF7331C9C4CD3; Path=/; HttpOnly
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 09 Jan 2019 18:50:18 GMT

{"id":1,"user":{"userName":"dveamer","pswd":"****"},"loggedIn":true}$ 
~~~

Cookie에 JSESSIONID를 입력하지 않고 또 ```/auth/login```로 로그인 요청을 했습니다. ```FA11A29DC084CCB0111BF7331C9C4CD3```이라는 새로운 JSESSIONID로 로그인 처리가 됐습니다.  


### 로그인 상태 재확인
 
~~~terminal
$ curl -i -H "Content-Type: application/json" --cookie "JSESSIONID=408495394AB2809A웃0EAE0D60056012BC; Path=/;" -X GET localhost:8080/auth/status

HTTP/1.1 200 
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 09 Jan 2019 18:50:45 GMT

{"loggedIn":false}$ 
~~~

최초의 JSESSIONID(```408495394AB2809A웃0EAE0D60056012BC```)로 로그인 상태를 확인해보면 로그아웃 처리된 것을 확인할 수 있습니다.  

~~~terminal
$ curl -i -H "Content-Type: application/json" --cookie "JSESSIONID=FA11A29DC084CCB0111BF7331C9C4CD3; Path=/;" -X GET localhost:8080/auth/status
HTTP/1.1 200 
Content-Type: application/json;charset=UTF-8
Transfer-Encoding: chunked
Date: Wed, 09 Jan 2019 18:57:22 GMT

{"id":1,"user":{"id":1,"userName":"dveamer","pswd":"****"},"loggedIn":true}$ 
~~~

새로운 JSESSIONID(```FA11A29DC084CCB0111BF7331C9C4CD3```)로 로그인 상태를 확인해보면 로그인 중인 것을 확인할 수 있습니다.  

## 결론

HTTP 호출을 이용한다는 좀 독특한 방법이지만 정상적으로 처리 되는 것을 확인했습니다.  

장점으로는 DB에 보관하고 WAS 메모리에는 추가적으로 보관하지 않아도 됩니다. 보관하는 기능을 잘못 구현하면 메모리 누수가 발생할 수 있지만 보관 자체가 불필요해집니다. 내용이 없다는 점과 HttpSession 객체를 가지고 있지 않음에도 불구하고 로그아웃 처리가 가능하기 때문에 꽤나 유연한 구조의 설계가 가능해진다는 점입니다.  

단점으로는 localhost를 호출하지 않을 경우, 내부 구간이겠지만 로그아웃 과정에서 네트워크를 타게 됩니다. 네트워크 이슈가 발생시 정확한 로그아웃 처리가 이뤄지지 않을 수 있기 때문에 이에대한 처리방안이 필요합니다.  

#### References

  * [Spring HttpSessionEventPublisher 소스코드](https://github.com/spring-projects/spring-security/blob/master/web/src/main/java/org/springframework/security/web/session/HttpSessionEventPublisher.java)
  * [Spring SessionRegistryImpl 소스코드](https://github.com/spring-projects/spring-security/blob/master/core/src/main/java/org/springframework/security/core/session/SessionRegistryImpl.java)

  * [session-timeout-and-concurrent-session-control-with-spring-security-and-spring-mvc](https://blog.trifork.com/2014/02/28/session-timeout-and-concurrent-session-control-with-spring-security-and-spring-mvc/)
  * [spring-session-concurrent-session-control](https://blog.trifork.com/2016/04/08/spring-session-concurrent-session-control/)

