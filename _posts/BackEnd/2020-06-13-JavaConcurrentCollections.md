---
layout: post
title: "Java Thread Safe Collections - List, Queue, Set, Map"
date: 2020-06-13 00:00:00
lastmod: 2020-06-13 00:00:00
categories: BackEnd
tags: BackEnd Java
---

![https://openjdk.java.net](https://openjdk.java.net/images/openjdk.png){:class="imgTitle"}  
( 이미지 출처 : [https://openjdk.java.net](https://openjdk.java.net) )  


Thread safe 한 Collection(List, Queue, Set) 그리고 Map의 구현체 사용법에 대해서 기술합니다.  

<!--more-->


# List

~~~java

    // JDK 1.2 이상에서 사용가능
    List<String> list0 = Collections.synchronizedList(new LinkedList<>());


    // JDK 1.5 이상에서 사용가능
    List<String> list1 = new CopyOnWriteArrayList();

~~~

Collections.synchronizedList 와 CopyOnWriteArrayList를 비교해보면,  

Collections.synchronizedList 는 list에 많은 데이터를 담을 때 유리하며 READ시 단일 쓰레드가 처리하기 때문에 느립니다.  
위의 예제처럼 ArrayList만이 아니라 LinkedList 등 List의 다양한 구현체들의 특성을 활용할 수 있습니다.  

CopyOnWriteArrayList는 쓰기 동작에서만 lock을 사용하고 읽기 동작은 lock 없이 사용 가능하다. 즉, 읽기가 많은 경우 사용하는 것이 좋다. 쓰기 동작에서는 데이터 복제도 일어나기 때문에 성능적으로 더욱 불리하다. 적은 양의 데이터를 다루는데 사용해야합니다.  


# Queue

~~~java

    // JDK 1.5 이상에서 사용가능
    Queue<String> queue = new ConcurrentLinkedQueue<>();

~~~


# Set 

~~~java

    // JDK 1.2 이상에서 사용가능
    Set<String> set0 = Collections.synchronizedSet(new HashSet<String>()); 

    // JDK 1.6 이상에서 사용가능 
    Set<String> set2 = Collections.newSetFromMap(new ConcurrentHashMap<String, Boolean>());

    // JDK 1.8 이상에서 사용가능 
    Set<String> set3 = ConcurrentHashMap.newKeySet();

~~~

Collections.synchronizedSet 과 ConcurrentHashMap을 이용한 Set을 비교해보면,  

Collections.synchronizedSet은 Set 전체에 대해 lock을 잡고 ConcurrentHashMap은 부분적으로 lock을 잡기 때문에 ConcurrentHashMap을 이용하는 것이 성능에 유리합니다.  
대신 Collections.synchronizedSet은 Set의 다양한 구현체들을 활용 가능합니다. 예를들어, [EnumSet](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/EnumSet.html)과 같은 Set도 적용 가능합니다.  


## SortedSet


~~~java

    // JDK 1.2 이상에서 사용가능
    Set<String> set1 = Collections.synchronizedSortedSet(new TreeSet<String>()); 

    // JDK 1.6 이상에서 사용가능 
    Set<String> set2 = new ConcurrentSkipListSet<>();

~~~

Collections.synchronizedSortedSet과 ConcurrentSkipListSet을 비교해보면,  

Collections.synchronizedSortedSet은 Set 전체에 대해 lock을 잡고 ConcurrentSkipListSet은 부분적으로 lock을 잡기 때문에 ConcurConcurrentSkipListSet이 성능에 유리합니다.  

# Map


~~~java

    // JDK 1.2 이상에서 사용가능 
    Map<String, String> map0 = Collections.synchronizedMap(new HashMap<String, String>());

    // JDK 1.5 이상에서 사용가능 
    Map<String, String> map1 = new ConcurrentHashMap<String, String>(); 

~~~

Collections.synchronizedMap과 ConcurrentHashMap을 비교해보면,  

Collections.synchronizedMap은 Map 전체에 대해 lock을 잡고 ConcurrentHashMap은 부분적으로 lock을 잡기 때문에 ConcurrentHashMap이 성능에 유리합니다.  
대신 Collections.synchronizedSet은 Map의 다양한 구현체들을 활용 가능합니다. 예를들어, [EnumMap](https://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/EnumMap.html)과 같은 Map도 적용 가능합니다.  

## SortedMap

~~~java

    // JDK 1.2 이상에서 사용가능 
    Map<String, String> map0 = Collections.synchronizedSortedMap(new TreeMap<>());

    // JDK 1.6 이상에서 사용가능 
    Map<String, String> map1 = new ConcurrentSkipListMap<>();

~~~


Collections.synchronizedSortedMap과 ConcurrentSkipListMap을 비교해보면,  

Collections.synchronizedSortedMap은 Map 전체에 대해 lock을 잡고 ConcurrentSkipListMap은 부분적으로 lock을 잡기 때문에 ConcurrentSkipListMap이 성능에 유리합니다.  


