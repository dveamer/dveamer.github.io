---
layout: post
title: "Java 변수 선언 & 초기화 방법 - Array, List, Set, Map"
date: 2019-04-14 00:00:00
lastmod: 2020-06-07 00:00:00
categories: BackEnd
tags: BackEnd Java
---

![https://openjdk.java.net](https://openjdk.java.net/images/openjdk.png){:class="imgTitle"}  
( 이미지 출처 : [https://openjdk.java.net](https://openjdk.java.net) )  


변수 선언과 동시에 초기화 작업 방법에 대해서 기술합니다.  

<!--more-->


# Array

~~~java
public class Sample {

    private final String[] values0 = new String[]{ "1", "2", "3" };

    // 위와 동일하지만 배열 선언시에만 사용 가능
    private final String[] values1 = { "1", "2", "3" };
}
~~~

# List

## 변경 가능한 List

~~~java
public class Sample {
    private final List<String> values0 = new ArrayList<>() {
        {
            add("1");
            add("2");
            add("3");
        }
    };

    private final List<String> values1 = new ArrayList<>(Arrays.asList(new String[]{ "1", "2", "3" }));

    // JDK 5 이상에서 사용 가능
    private final List<String> values2 = new ArrayList<>(Arrays.asList("1", "2", "3"));
}
~~~

## Immutable List

초기화 이후에 변경이 불가한 list를 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

~~~java
public class Sample {

    private final List<String> values0 = Arrays.asList(new String[]{ "1", "2", "3" });

    private final List<String> values1 = Collections.unmodifiableList(new ArrayList<>(values0));

    // JDK 5 이상에서 사용 가능
    private final List<String> values2 = Arrays.asList("1", "2", "3");

    // JDK 9 이상에서 사용 가능
    private final List<String> values3 = List.of("1", "2", "3");
}
~~~

# Set

## 변경 가능한 Set

~~~java
public class Sample {
    private final Set<String> values = new HashSet<>(Arrays.asList("1", "2", "3"));
}
~~~

## Immutable Set

초기화 이후에 변경이 불가한 set을 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

~~~java
public class Sample {

    private final Set<String> values0 = Collections.unmodifiableSet(new HashSet<String>(Arrays.asList("1", "2", "3")));

    // JDK 9 이상에서 사용 가능
    private final Set<String> values1 = Set.of("1", "2", "3");
}
~~~

<!--ads-->

# Map

## 변경 가능한 Map

~~~java
public class Sample {
    private final Map<String, String> map = new HashMap<String, String>() {
        {
            put("key01", "val01");
            put("key02", "val02");
            put("key03", "val03");
        }
    };
}
~~~

## Immutable Map

초기화 이후에 변경이 불가한 Map을 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

Collections 는 Collection interface에 대한 utility class입니다.  
근데 Map interface는 Collection interface를 extend 하지 않음에도 불구하고 Collections으로부터 몇가지 기능을 지원받고 있습니다.  

~~~java
public class Sample {
    private final Map<String, String> map0 = Collections.unmodifiableMap(new HashMap<String, String>() {
        {
            put("key01", "val01");
            put("key02", "val02");
            put("key03", "val03");
        }
    });

    // JDK 9 이상에서 사용 가능
    // 10개의 key, value까지만 사용 가능
    Map<String, String> map1 = Map.of("key01","value01", "key02", "value02");
}
~~~


# Empty Immutable Collection

항상 텅비어있는 상태를 유지하는 List, Set, Map을 만드는 방법입니다.  
프로세스에 유일하게 1개만 존재하기 때문에 자원 관점에서 굉장히 효율적입니다.  
불변 객체이기 때문에 변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

~~~java
public class Sample {

    // JDK 5 이상에서 사용 가능
    private final List<String> emptyList0 = Collections.emptyList();
    private final Set<String> emptySet0 = Collections.emptySet();
    private final Map<String, String> emptyMap0 = Collections.emptyMap();

    // JDK 9 이상에서 사용 가능
    private final List<String> emptyList1 = List.of();
    private final Set<String> emptySet1 = Set.of();
    private final Map<String, String> emptyMap1 = Map.of();
}
~~~







