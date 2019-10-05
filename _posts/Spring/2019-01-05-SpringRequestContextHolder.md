---
layout: post
title:  "Spring RequestContextHolder - 어디서든 HttpServletReqeust 사용하기"
date:   2019-01-05 12:00:00 
lastmod: 2019-10-05 00:00:00  
categories: BackEnd
tags: Spring 
---

![https://spring.io/](https://spring.io/img/spring-by-pivotal.png){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

Spring RequestContextHolder 를 이용해서 controller, service, DAO 전 구간에서 HttpServletRequest에 접근하는 방법을 설명드립니다.  

<!--more-->

이 글은 [Spring @Async Session](/BackEnd/SpringAsyncSession.html) 글을 부연설명하기 위해 작성되었습니다.  
[Spring @Async Session 샘플](https://github.com/dveamer/SpringBootSample/tree/master/AsyncSession)을 참고하시면 이해하시는데 도움이 되실 겁니다.  

# HttpServletReqeust

## HttpServletReqeust 접근 목적

HttpServletReqeust는 자주 접하게 되는 객체입니다. 특히 servlet, filter 개발을 하게 되면 정말 자주 접하게 됩니다.  
Spring을 사용해서 개발을 할 때도 interceptor, AOP, controller 에서 HttpServletReqeust 사용이 필요할 때가 많습니다.  

HttpServletReqeust를 통해 요청받은 HTTP URI, HTTP method, HTTP body 등을 사용할 수 있고 HTTP header에서 cookie를 확인할 수도 있습니다.  

그리고 HTTP session을 사용할 수도 있습니다. HTTP session 사용의 가장 대표적인 예로 로그인, 로그인 여부 체크, 로그아웃 등이 있겠죠.  

~~~java

    ...(생략)

    private static final String USER_ID = "USER_ID"

    ...(생략)

    public void success(HttpServletRequest req, String userId) {
        HttpSession session = httpServletRequest.getSession();
        session.setAttribute(USER_ID, userId);
    }

    public String getUserId(HttpServletRequest req) {
        HttpSession session = httpServletRequest.getSession();

        try{
            return (Stirng) session.getAttribute(USER_ID);
        }catch(Exception ex){
            return null;
        }
    }

    ...(생략)

~~~

WAS간의 http session 공유 방법은 HttpServletReqeust에서 제공하는 HttpSession 객체를 사용하는 것 외에도 다른 방법들이 있습니다.  
특히 최근에는 in-memory cache solution을 이용한 session manager 서버를 별도로 구성하는 방법을 많이 사용합니다. 이런 경우 HttpSession 객체가 불필요해 지는 경우가 있습니다.  
하지만 http session을 공유하는 방법에 정답이 있는 것도 아니고 프로젝트의 상황에 따라 상황이 달라지기 때문에 알아두는 것이 필요합니다.  


# Spring에서 HttpServletReqeust 접근

Java에서 HttpServletReqeust에 대한 접근은 제한이 있습니다. servlet, filter, interceptor, AOP, controller 정도에서만 접근이 가능합니다. 만약 service에서 사용하길 원한다면 controller에서 service로 파라미터로 전달해야할 것입니다. 이것은 권장되는 모양새는 아니고 보통 http session에 보관할 VO(Valud Object)를 만들고 VO를 service로 전달합니다. 그 결과 모든 controller에서는 HttpSession에서 VO를 추출하는 작업을 하고 모든 service는 method는 VO를 파라미터로 받을 수 있도록 만들어져야 합니다.  

Spring에서는 controller에서 작업 없이 그리고 service method에서 해당 VO를 파라미터로 받지 않고서도 해당 VO에 접근할 방법을 제공합니다.  

덕분에 반복되는 파라미터 전달을 줄이는 것 외에도 유용한 기능들을 만들 수 있습니다.  
예를들어, 로그인시에 생성한 loggingCode를 HttpSerssion에 보관하고 로그를 파일에 쓸 때 패턴으로 loggingCode를 남길수 있습니다.  
HttpSession에다가 보관했기 때문에 같은 사용자라면 모든 WAS의 로그에서 같은 loggingCode가 패턴으로 출력됩니다. 로드밸런싱에 의해서 여러 서버에 남겨지고 수많은 사용자들의 로그에 묻힌 특정 사용자의 로그를 추적하는데 유용하겠죠?  

## Spring RequestContextHolder

Spring RequestContextHolder은 Spring 2.x부터 제공되던 기능으로 controller, service, DAO 전 구간에서 HttpServletRequest에 접근할 수 있도록 도와줍니다.  

원리를 단순화하여 설명하자면, Spring은 static한 map을 하나 만들어 놓고 servlet이 호출되면 key를 thread로 해서 제공받은 HttpServletRequest을 보관합니다. 그리고 servlet이 종료 될 때 해당 thread를 key로 갖는 HttpServletRequest을 map에서 제거합니다. 그 결과, 호출된 servlet과 동일한 thread내에서는 어느 곳에서든 같은 HttpServletRequest를 꺼내 쓸 수 있게됩니다. 그리고 controller 에서 추가적인 작업을 하지 않아도 service, DAO 등에서 HttpServletRequest에 접근이 가능해집니다.  

방금 설명한 내용은 단순화한 설명이지 실제와는 다릅니다. 실제는 ThreadLocalMap 이라는 객체가 사용되고 이는 Map의 구현체가 아닙니다. 또한 보관하는 value는 HttpServletRequest 그 자체가 아니라 가공된 다른 것입니다.  

Spring에서 HttpServletRequest 접근 방법은 아래와 같습니다.  

~~~java
    HttpServletRequest req = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
~~~

### 주의사항 

주의할 사항은 session scope에 너무 많은 정보를 보관하면 안된다는 점입니다.  
HTTP session을 WAS간에 공유하기 위해 session clustering이 이뤄지는데 정보가 많아지면 WAS에 부하를 줍니다.  
정보 1개를 추가하더라도 사용자가 1만명이면 1만개의 정보가 추가 된다고 생각하시면 됩니다.  
그리고 WAS의 개수가 많아질 수록 WAS에 부하가 심하게 늘어납니다. sigma(n-1) 의 부하량이 예상되고 WAS가 2대에서 3대로 증가하면 2만큼의 부하량이 증가되지만 10대에서 11대로 증가하면 10만큼의 부하량이 증가되는 것입니다. WAS가 3대일 때는 부하량을 3이라고 한다면 WAS가 10대 일 때는 부하량이 45 정도 될 것입니다.  

그리고 controller와 service간의 파라미터를 주고 받는 대용으로 사용하라는 이야기는 절대 아닙니다.  
WAS간의 공유가 필요한 정보를 이 방법을 이용하면 cotnroller, service, DAO 전 구간에서 쉽게 접근이 가능하다는 이야기입니다.  
그리고 WAS간의 공유가 불필요하지만 thread 내에서 공통적으로 자주 사용되는 내용은 session scope이 아니라 requset scope을 이용하면 WAS에 무리를 주지 않고도 전 구간에서 접근이 가능합니다.  

# HttpServletReqeust Util

별 내용은 없지만 Spring RequestContextHolder을 좀 더 쉽게 이용하고 유연성을 제공하기 위한 유틸리티를 만들어 사용하면 좋습니다.  

아래는 SessionScopeUtil, RequestScopeUtil 이라는 유틸리티를 예시입니다.  

## SessionScopeUtil

Spring RequestContextHolder를 활용하여 Controller-Service-Dao 전구간에서 HttpServletRequest의 Session에 보관해둔 정보를 사용할 수 있도록 해주는 유틸리티 입니다.  
HttpServletRequest 의 Session에 보관되는 정보이기 때문에 다른 WAS에서도 동일한 SESSION_ID만 가지고 있다면 동일한 attribute를 꺼내서 사용할 수 있습니다.  

~~~java
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

public class SessionScopeUtil {

    public static Attribute getAttribute() {
        return (Attribute) RequestContextHolder.getRequestAttributes().getAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
    }

    public static void setAttribute(Attribute attribute) {
        RequestContextHolder.getRequestAttributes().setAttribute(Attribute.KEY, attribute, RequestAttributes.SCOPE_SESSION);
    }

    public static void removeAttribute() {
        RequestContextHolder.getRequestAttributes().removeAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
    }
}
~~~

예를들어, 아래와 같이 setAttribute()에서는 Java에서 제공하는 HttpSession 객체를 이용한 방법을 사용하고 getAttribute()에서는 Spring에서 제공한 방법을 사용해도 정상적으로 attribute를 받아낼 수 있습니다.  

~~~java
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

public class SessionScopeUtil {

    public static Attribute getAttribute() {
        return (Attribute) RequestContextHolder.getRequestAttributes().getAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
    }

    public static void setAttribute(Attribute attribute) {
        HttpServletRequest req = ((ServletRequestAttributes)RequestContextHolder.getRequestAttributes()).getRequest();
        HttpSession session = req.getSession();
        session.setAttribute(Attribute.KEY, attribute);

        //RequestContextHolder.getRequestAttributes().setAttribute(Attribute.KEY, attribute, RequestAttributes.SCOPE_SESSION);
    }

    public static void removeAttribute() {
        RequestContextHolder.getRequestAttributes().removeAttribute(Attribute.KEY, RequestAttributes.SCOPE_SESSION);
    }
}
~~~

## RequestScopeUtil

Spring RequestContextHolder를 활용한 유틸리티 입니다.  
Controller와 동일한 thread 내에서는 Controller-Service-Dao 전구간에서 HttpServletRequest에 보관해둔 정보를 사용할 수 있습니다.  

~~~java
import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;

public class RequestScopeUtil {

    public static Attribute getAttribute() {
        return (Attribute) RequestContextHolder.getRequestAttributes().getAttribute(Attribute.KEY, RequestAttributes.SCOPE_REQUEST);
    }

    public static void setAttribute(Attribute attribute) {
        RequestContextHolder.getRequestAttributes().setAttribute(Attribute.KEY, attribute, RequestAttributes.SCOPE_REQUEST);
    }

    public static void removeAttribute() {
        RequestContextHolder.getRequestAttributes().removeAttribute(Attribute.KEY, RequestAttributes.SCOPE_REQUEST);
    }
}
~~~





