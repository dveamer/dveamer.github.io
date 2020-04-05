---
layout: post
title: "Spring @Cacheable Cache 처리"
date: 2018-09-23 00:00:00
last_mod: 2018-10-20 00:00:00
categories: BackEnd
tags: Spring Cache AOP
---

![https://spring.io/](https://spring.io/images/spring-logo-fc4350c59999bb62c468361537212419.svg){:class="imgTitle"}  
( 이미지 출처 : [https://spring.io/](https://spring.io/) )  

이 글은 Spring을 사용하면 Redis, Memcached, Ehcache 와 같은 다양한 cache 솔루션 사용이 편해지는지를 설명합니다.  
Ehcache 솔루션을 기준으로 Spring @Cacheable을 설명하겠습니다.  

<!--more-->

SpringBoot 를 사용했으며 java based configuration 방식을 이용했습니다.  

최종 결과물에 대한 [샘플](https://github.com/dveamer/SpringBootSample/tree/master/Cache)을 Github에 올려뒀으니 참고하시기 바랍니다.  


# 업무로직

권한별 Menu 리스트를 조회하는 내용의 retrieveMenus 메소드가 있습니다.  

~~~java
public class Menu4JavaServiceImpl implements MenuService {

    ...(생략)

    @Override
    public List<Menu> retrieveMenus(Auth auth) throws Exception {

        List<Menu> menus = null;

        // do something..

        return menus;
    }

}
~~~

이 소스코드에 순수 Java 방식과 Spring @Cacheable 방식으로 각각 캐싱 처리를해서 비교해보겠습니다.  


# 순수 Java 캐싱 처리 


```Menu4JavaServiceImpl.java```

~~~java
import java.net.URL;
import net.sf.ehcache.Cache;
import net.sf.ehcache.CacheManager;
import net.sf.ehcache.Element;

...(생략)

public class Menu4JavaServiceImpl implements MenuService {

    ...(생략)

    CacheManager cacheManager = new CacheManager(getClass().getResource("/ehcache.xml"));

    @Override
    public List<Menu> retrieveMenus(Auth auth) throws Exception {

        final Cache cache = cacheManager.getCache("menuCache");

        List<Menu> menus = null;
        try{
            Element element = cache.get(auth);
            if(element!=null){
                return (List<Menu>) element.getObjectValue();
            }
        }catch(Exception ex){
            logger.info(ex.getMessage());
        }

        // do something..

        cache.put(new Element(auth, menus));

        return menus;
    }
}
~~~

캐싱을 하기 위해서는 업무로직이 담긴 ```retrieveMenus``` 메소드 수정을 해야만 합니다.  

이것을 통해 추가적인 문제점을 3개 정도 예상할 수 있습니다.  

  * 캐싱해야하는 메소드가 여러개라면 모든 메소드의 업무로직을 수정해야합니다.  
  * Ehcache가 아닌 다른 솔루션을 사용하게 된다면 모든 메소드의 업무로직을 수정해야 합니다.  
  * 업무로직의 input parameter, return value가 바뀐다면 캐싱 관련 로직도 함께 수정되어야 합니다.  

Spring @Cacheable과 비교하기 위해서 위의 세가지 문제점을 기억해주시기 바랍니다.  


# Spring @Cacheable

Spring의 @Cacheable annotation을 이용하면 간단하게 처리가 가능합니다.  
  

## 사전작업 

Application 클래스에 @EnableCaching을 설정하고 application.properties에 ehcache.xml의 위치를 작성합니다.  

```Application.java```

~~~java
...(생략)

@EnableCaching
@SpringBootApplication
public class Application {
    ...(생략)
}
~~~

```application.properties```

~~~properties
spring.cache.ehcache.config=classpath:ehcache.xml
~~~

## Service 객체 

메소드 위에 @Cacheable annotation만 추가하면 method는 캐싱 처리됩니다.  


```MenuServiceImpl.java```

~~~java
public class MenuServiceImpl implements MenuService {

    @Override
    @Cacheable(value = "menuCache", key = "#auth")
    public List<Menu> retrieveMenus(Auth auth) throws Exception {

        List<Menu> menus = null;

        // do something..

        return menus;
    }
}
~~~


### 업무로직과 캐싱로직의 완벽한 분리  

프로그램을 설계할 때 caching이 필요한 상황, 영역을 정확하게 구별해 낼 수 있을까요?  

Cache는 성능을 높일 방법이긴 하지만 잘못 사용하면 데이터 정합성이 깨지는 문제를 발생시키기 때문에 반드시 필요한 영역에만 적용해야합니다. 그래서 개발 막바지 과정, 테스팅 과정에서 caching이 필요한 영역을 확정하고 적용하는 것이 바람직하다고 생각합니다. 운영 과정에서 추가적으로 필요한 영역이 발견되면 추가 적용하고 불 필요해진 영역이 발견되면 제거도 해야겠죠.  

그렇다면 이미 작성된 업무로직을 수정하는 것은 굉장히 위험한 작업이 될 수 있습니다. 즉, Caching을 언제든 추가/제거 하더라도 업무로직 소스코드에는 영향이 없도록 caching과 업무로직을 분리시키는 것이 중요합니다.  

위의 샘플처럼 Spring @Cacheable을 사용하면 업무로직과 캐싱 설정은 완전히 분리됩니다.  

참고로, 특정 관점(Aspect)과 업무로직을 분리시키는 것을 추구하는 프로그래밍 방식을 AOP(Aspect Oriented Programing)라고 하며 @Cacheable이 설정되어있는 메소드는 Spring AOP를 통해 캐싱 처리됩니다.  

### 업무로직 수정없는 솔루션 교체  
 
Ehcache가 아닌 Redis와 같은 다른 솔루션으로 교체하더라도 업무로직은 수정이 불필요합니다.  
Spring, Redis간의 연동설정은 필요하고 @Cacheable annotation의 파라미터 등이 수정은 필요합니다.  

Ehcache, Redis 특성에 의해 캐싱할 영역에 대한 재 설계가 필요할 수도 있으나 이는 순수 Java, Spring @Cacheable과 무관한 내용입니다.  

### 업무로직 변경에 쉽게 대응  

업무로직이 변경되는 것과 return value가 변하는 것에 대해서는 Spring @Cacheable은 변경될 사항이 없습니다.  
Input parameter가 바뀌거나 개수가 바뀐다면 Spring @Cacheable annotation의 key parameter만 수정해주시면 됩니다.   

## 주의사항

Spring @Cacheable은 내부적으로 Spring AOP를 이용하기 때문에 @Async, @Transactional 등과 마찬가지로 아래와 같은 제약사항을 갖습니다.  

  * pulbic method에만 사용가능 합니다.  
  * 같은 객체내의 method끼리 호출시에는 @Cacheable이 설정되어있어도 캐싱되지 않습니다.  

AspectJ를 이용하면 제약사항을 회피하기 가능하며 그에 대해서는 추후에 작성하도록 하겠습니다.  
동일한 원인과 해법에 대해서 다룬 [Spring @Async AspectJ 비동기처리](/java/SpringAsyncAspectJ.html) 글을 읽어보시면 도움이 되실겁니다.  



# Ehcache 설정

위의 순수 Java 예제와 Spring @Cacheable 예제 모두 Ehcache 설정이 필요합니다.  
사실 가장 먼저 설정되어야 하는 사항이지만 이 글의 문맥상 중요한 사항이 아니고 Ehcache가 아닌 Redis 같은 다른 솔루션을 사용시에는 바뀌어야 하는 내용이므로 글의 가장 하단에 작성했습니다.  

## Dependancy 추가 

```build.gradle```

~~~gradle
....(생략)
buildscript {
    ....(생략)
    ext {
        ....(생략)
        ehcacheVersion = '2.10.3'
    }
}


dependencies {
    ....(생략)
    // Ehcache
    compile("net.sf.ehcache:ehcache:${ehcacheVersion}")
}
....(생략)
~~~



## Ehcache Configruation 작성

Classpath 위치에 ```ehcache.xml``` 파일을 작성해둡니다.  
Spring의 경우 ```/src/main/java/resources/``` 아래에 두면 됩니다.  

```ehcache.xml```

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://www.ehcache.org/ehcache.xsd"
         updateCheck="true"
         monitoring="autodetect"
         dynamicConfig="true">

    <diskStore path="io.dveamer.sample.ehcache"/>

    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            overflowToDisk="true"
            maxElementsOnDisk="10000000"
            diskPersistent="false"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU"
    />

    <cache name="menuCache"
           maxElementsInMemory="10"
           eternal="false"
           overflowToDisk="false"
           timeToIdleSeconds="300"
           timeToLiveSeconds="600"
           memoryStoreEvictionPolicy="LRU" />

</ehcache>
~~~

## 기타 사항

## Cache 관련 다른 annotation

  * @CachePut : cache를 갱신할 때 사용
  * @CacheEvict : cache를 삭제할 때 사용
  * @Caching : 여러개의 @CacheEvict 혹은 @CachePut 을 하나의 메소드에 걸어야할 때 사용

메뉴정보를 캐싱하고 있는데 간혹 변경이 필요하다면 @CachePut을 이용해서 변경을 할 수 있습니다.  

하지만 @CachePut, @CacheEvict를 사용하지 않고서도 충분히 캐싱을 잘 활용할 수 있다고 생각합니다.  
예를들어 변경되는 일시를 컨트롤할 수 있다면, 서버 배포와 맞물려서 캐싱된 정보가 초기화되도록 처리할 수도 있을 것입니다.  

빈번한 변경이 일어나는 데이터의 경우에는 캐싱의 필요여부부터 다시 고민해봐야할 것 같습니다.  

### 주의사항 

  * @CachePut과 @Cacheable을 하나의 메소드에 걸어서는 안됨
  * WAS가 여러대이고 Ehcache가 클러스터링이 안되어있다면 @CachePut과 @Cacheable 모든 WAS에서 실행을 시켜줘야함

## @Cacheable KeyGenerator

int, long 과 같은 primitive type의 파라미터만 사용하는 경우가 아니고  
VO(또는 Map)를 파라미터로 사용한다면 VO가 가진 모든 feild 값을 키로 사용하는 경우는 많지 않을 겁니다.  

그럴 때는 아래와 같은 방법으로 키를 생성해서 사용할 수 있습니다.  

~~~java
public class MenuServiceImpl implements MenuService {

    @Override
    @Cacheable(value = "menuCache", key = "T(com.dveamer.sample.KeyGen).generate(#auth)")
    public List<Menu> retrieveMenus(Auth auth) throws Exception {

        List<Menu> menus = null;

        // do something..

        return menus;
    }
}
~~~

~~~java
package com.dveamer.sample;

public class KeyGen {
    public static Object generate(Auth auth) {
        return auth.getAuthCd() + ":" + auth.getSystem().getSystemCd();
    }
}
~~~


#### References

  * [http://www.ehcache.org/](http://www.ehcache.org/)
  * [Spring-Cache-키-생성-유의사항](http://knight76.tistory.com/m/entry/Spring-Cache-%ED%82%A4-%EC%83%9D%EC%84%B1-%EC%9C%A0%EC%9D%98%EC%82%AC%ED%95%AD)

