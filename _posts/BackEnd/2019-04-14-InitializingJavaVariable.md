---
layout: post
title: "Java 변수 선언 & 초기화 방법 - Array, List, Set, Map"
date: 2019-04-14 00:00:00
categories: BackEnd
tags: BackEnd Java
---

변수 선언과 동시에 초기화 작업 방법에 대해서 기술합니다.  
매번 검색하기도 지겹고 제대로 된 방법인지 생각해보는 단계를 없애기 위해서 직접 기록합니다.  

<!--more-->


# Array

~~~java
public class Sample {

    private final String[] variables0 = new String[]{ "1", "2", "3" };

    // 위와 동일하지만 배열 선언시에만 사용 가능
    private final String[] variables1 = { "1", "2", "3" };
}
~~~

# List

## 변경 가능한 List

~~~java
public class Sample {
    private final List<String> variables0 = new ArrayList<String>() {
        {
            variables.add("1");
            variables.add("2");
            variables.add("3");
        }
    };

    private final List<String> variables1 = Arrays.asList(new String[]{ "1", "2", "3" });

    // JDK 5 이상에서 사용 가능
    private final List<String> variables2 = Arrays.asList("1", "2", "3");
}
~~~

## Immutable List

초기화 이후에 변경이 불가한 list를 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

~~~java
public class Sample {

    private final List<String> variables0 = Collections.unmodifiableList(Arrays.asList("1", "2", "3"));

    // JDK 9 이상에서 사용 가능
    private final List<String> variables1 = List.of("1", "2", "3");
}
~~~

# Set

## 변경 가능한 Set

~~~java
public class Sample {
    private final Set<String> variables = new HashSet<>(Arrays.asList("1", "2", "3"));
}
~~~

## Immutable Set

초기화 이후에 변경이 불가한 set을 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

~~~java
public class Sample {

    private final Set<String> variables0 = Collections.unmodifiableSet(new HashSet<>(Arrays.asList("1", "2", "3")));

    // JDK 9 이상에서 사용 가능
    private final Set<String> variables1 = Set.of("1", "2", "3");
}
~~~

# Map

## 변경 가능한 Map

~~~java
public class Sample {
    private final Map<String, String> variables = new HashMap<>() {
        {
            variables.put("key01", "val01");
            variables.put("key02", "val02");
            variables.put("key03", "val03");
        }
    };
}
~~~

## Immutable Map

초기화 이후에 변경이 불가한 set을 만드는 방법입니다.  
변경 시도시에는 UnsupportedOperationException 이 발생합니다.  

Collections 는 Collection interface에 대한 utility class입니다.  
근데 Map interface는 Collection interface를 extend 하지 않음에도 불구하고 Collections으로부터 몇가지 기능을 지원받고 있습니다.  

~~~java
public class Sample {
    private final Map<String, String> variables0 = Collections.unmodifiableMap(new HashMap<>() {
        {
            variables0.put("key01", "val01");
            variables0.put("key02", "val02");
            variables0.put("key03", "val03");
        }
    });

    // JDK 9 이상에서 사용 가능
    // 10개의 key, value까지만 사용 가능
    Map<String, String> variables1 = Map.of("key01","value01", "key02", "value02");
}
~~~


# Empty Immutable Collection

항상 비어있는 Collection을 여러 thread에서 필요시마다 계속 생성하는 것은 효율적이지 못한 방법입니다.  
아래 방법은 process에 유일하게 1개 존재하는 empty-immutable-collection을 모든 thread에서 공유할 수 있도록 해줍니다.  

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







