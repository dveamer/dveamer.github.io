---
layout: post
title:  "Code Visualization Tools"
date:   2015-12-01 01:00:00
categories: Java
tags: ObjectAid Java-Docs Eclipse Class-Diagram
---

![ObjectAid](http://www.objectaid.com/static/images/java-perspective.png;jsessionid=38E7A9419700C4B4FD0102256016089D)

( 이미지 출처 : [http://www.objectaid.com](http://www.objectaid.com) )

ObejctAid 는 클래스 다이어그램과 시퀀스 다이어그램을 소스코드로부터 추출해내는 툴이다.  
Eclipse 플러그인 형태로 제공되며 Java 소스코드와 라이브러리의 다이어그램을 추출 가능하다.  

다이어그램은 소스코드로부터 추출되어져야만 한다고 생각한다.

새로운 시스템을 만났을 때  
산출물의 존재여부는 시스템 파악의 진척률에 매우 큰 영향을 준다.  
또한 산출물의 신뢰도 역시 시스템 파악의 진척률에 매우 큰 영향을 준다.

시스템이라는 실체로부터 추출 될 수 있는 산출물이 실제 업무에 사용할 수 있는 신뢰성 높은 자료라고 생각한다.

게다가 시스템으로부터 추출 가능한 산출물을 기반으로 작업을 하게 되면  
매 상황마다 산출물 추출이 용이하며 그 위에 간략하고 빠르게 설계를 진행할 수 있으므로  
애자일 방법론으로 프로젝트를 진행하는 과정에서 전체 사이클이 더 빠르게 돌아갈 수 있다.

<!--more-->

최근에는 분산처리, 마이크로시스템 아키텍쳐 등과 같이   
시스템의 종류가 많아졌고 개수가 동적으로 변하기도 한다.  
그에 따라 시스템 구성도가 복잡해지고 수시로 변하게 되는데   
이러한 시스템 구성도 뿐만 아니라 처리현황을 알려주는 모니터링 솔루션도 존재한다.


클래스 다이어그램이 필요할 경우 ObjectAid 를 사용중이다.  
예전에는 Architexa 를 활용했지만 계정에 문제가 생겨서 활용에 실패한 경험이 있은 후 사용해보질 않았다.

현재까지는 무료기능만 사용해나가고 있는데 아마도 조만간 유료 라이센스를 구매할 것 같다.  
유료 라이센스 사용시에는 "Show Call Hierarchy for Method" 기능을 사용할 수 있는데   
클래스, 메소드들간의 상관관계를 다이어그램에서 추출가능하므로 소스분석시 유용하다.   

개인적으로 시퀀스 다이어그램은 거의 활용하지 않는다.

# ObjectAid
 * http://www.objectaid.com/
 * 클래스 다이어그램, 시퀀스 다이어그램 제공
 * Eclipse plugin 형태로 제공
 * JAVA 만 사용가능

# Java docs
  * 소스코드의 내용과 주석들을 가지고 API 문서를 작성하는 방법입니다.
  * 이클립스의 기본 기능을 활용해서 사용가능하다. ( 다른 IDE에서도 당연히 될 것으로 예상된다. )
  * 객체명, 메소드명 그리고 주석에 평소 신경을 써두면 문서 작성에 추가적으로 힘을 쓸 필요가 없다.
  * 주석 템플릿 설정을 해두면 유용하다.

## How to make it
  * Eclipse > Project > Generate JavaDoc 
    - javadoc command 세팅 : $JAVA_HOME/bin/javadoc
    - document를 만들 프로젝트 선택
    - Next 클릭
