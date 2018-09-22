---
layout: post
title: "Mybatis Cache 제거 방법"
date: 2018-09-22 00:00:00
categories: BackEnd
tags: Mybatis
---

![Mybatis Logo](http://3.bp.blogspot.com/-HKtWXLIvvdk/T6VWCexS-qI/AAAAAAAAATo/QmRUDiFjWd0/s1600/mybatis-superbird-small.png){:class="imgTitle"}  
(이미지 출처 : [Mybatis](http://www.mybatis.org))  

Mybatis의 cache 기능은 설정하지 않으면 기본이 on 상태입니다.  

개인적으로 기본설정은 캐싱없이 진행하고 추후 성능의 문제가 발생하면 캐싱을 추가하는 것이 맞다고 생각합니다.  
근데 Mybatis는 캐싱을하는 것이 기본설정이라는 점이 좀 재미있습니다.  
캐싱이 되고 있는지 모르는 상태에서 시스템을 구성한다면 다양한 문제를 만날 수 있을 텐데요..  

이 글에서는 캐싱 기능을 종료하는 방법을 설명합니다.  

<!--more-->

# 결론

Mybatis configuration.xml 파일에 ```cacheEnable```, ```localCacheScope``` 설정을 추가/변경 합니다.  

```configuration.xml```  

~~~xml
<settings>
  <setting name="cacheEnabled" value="false"/>
  <setting name="localCacheScope" value="STATEMENT"/>
  ...(생략)
</settings>
~~~

개인적인 의견으로는 Mybatis의 캐싱기능은 모두 종료시키고  
추후 성능문제로 캐싱 기능이 필요하다면 Mybatis가 아닌 Redis, ehcache와 같은 별도의 솔루션을 사용하는 것이 좋습니다.  

# 설명

## cacheEnabled

>  Globally enables or disables any caches configured in any mapper under this configuration.  
>  
>  설정에서 각 매퍼에 설정된 캐시를 전역적으로 사용할지 말지에 대한 여부  
>  
> 출처 : [Mybatis Configuration - Settings](http://www.mybatis.org/mybatis-3/configuration.html#settings), [Mybatis Configuration (Korean) - Settings](http://www.mybatis.org/mybatis-3/ko/configuration.html#settings)  

Mybatis를 사용할 때 여러개의 SqlSession를 정의해서 사용할 수 있고 각 SqlSession에게는 datasource, configuration.xml, mapper 등을 다르게 설정 할 수 있습니다.  

```cacheEnabled```를 ```true```로 설정한다는 것은 다른 SqlSession을 사용하더라도 동일한 SQL이라면 캐싱을 하겠다는 설정입니다.  
여러 사용자의 동일한 SQL을 호출에 대해서 캐싱된 동일한 결과를 받게 됩니다. 재미있는 점은 캐싱 시간 설정이 없습니다.  

기본값은 ```true``` 이고 캐싱을 방지하기 위해서는 ```false```로 설정하셔야 합니다.  


변하지 않는 DB 데이터가 있다면,  
해당 내용에 대한 조회 SQL을 특정 mapper로 모아놓고 해당 mapper를 사용하는 특정 SqlSession을 만들어서  
해당 SqlSession은 ```cacheEnabled```를 ```true``` 설정하고 사용하게 하면 의미있는 사용방법이 될 것 같긴합니다.  


## localCacheScope 

>  MyBatis uses local cache to prevent circular references and speed up repeated nested queries. By default (SESSION) all queries executed during a session are cached. If localCacheScope=STATEMENT local session will be used just for statement execution, no data will be shared between two different calls to the same SqlSession.  
>  
>  마이바티스는 순환참조를 막거나 반복된 쿼리의 속도를 높히기 위해 로컬캐시를 사용한다. 디폴트 설정인 SESSION을 사용해서 동일 세션의 모든 쿼리를 캐시한다. localCacheScope=STATEMENT 로 설정하면 로컬 세션은 구문 실행할때만 사용하고 같은 SqlSession에서 두개의 다른 호출사이에는 데이터를 공유하지 않는다.  
>  
> 출처 : [Mybatis Configuration - Settings](http://www.mybatis.org/mybatis-3/configuration.html#settings), [Mybatis Configuration (Korean) - Settings](http://www.mybatis.org/mybatis-3/ko/configuration.html#settings)  

```cacheEnabled``` 설정을 ```false```로 해도 캐싱이 되는 경우가 있습니다.  
```localCacheScope```이 ```SESSION```으로 설정되어있다면 하나의 SqlSession 을 가지고 동일한 SQL을 반복 호출 시 캐싱된 동일한 결과를 받게 됩니다.  

일반적인 상황은 아니고 마주치기 어려운 상황이긴 합니다.  
예를들어, 멀티 프로세스로 비동기처리를하고 메인 프로세스가 작업의 완료 여부를 반복적인 DB조회를 통해서 체크한다고 하겠습니다. 모든 비동기처리가 완료되어 DB의 데이터는 처리완료상태가 되었어도 메인 프로세스는 계속 캐싱된 결과를 조회하기 때문에 문제가 발생합니다.  

재미있는 점은 ON/OFF 설정이 없습니다. 무조건 캐싱되고 scope을 ```SESSION```, ```STATEMENT``` 두개로 나눠져있습니다.  

```SESSION```으로 설정하면 동일한 SqlSession으로 반복 조회를 하면 계속 캐싱된 결과를 받게 됩니다. 매 조회마다 새로운 SqlSession을 받아서 처리하거나 SqlSession을 매번 clear 하지 않는한은 계속 캐싱된 결과를 받게 될 것입니다.  
```STATEMENT```로 설정하면 동일한 SqlSession이더라도 각 SQL조회마다 캐싱이 이뤄지기 때문에 정상적으로 비동기 작업의 처리완료 여부를 체크할 수 있습니다.
  
기본값은 ```SESSION```이고 캐싱을 방지하기 위해서 ```STATEMENT```로 설정하셔야 합니다.

단순한 SQL만을 사용했다면 ```STATEMENT``` 설정은 사실상 OFF 설정으로 봐도 됩니다.  
다만, 단순한 SQL이 아니라 procedure 사용과 같은 케이스는 피하고자하는 설계이기 때문에 경험이 없고 캐싱이 어떻게 처리 되는지 잘 모르겠습니다.  

#### Reference

  * [Mybatis Configuration](http://www.mybatis.org/mybatis-3/configuration.html)
  * [Mybatis Configuration (Korean)](http://www.mybatis.org/mybatis-3/ko/configuration.html)


