---
layout: post_en
title: "How to initialize Java variables - Array, List, Set, Map"
date: 2019-09-12 00:00:00
lastmod: 2020-06-07 00:00:00
categories: BackEnd
tags: BackEnd Java
---

![https://openjdk.java.net](https://openjdk.java.net/images/openjdk.png){:class="imgTitle"}  
( 이미지 출처 : [https://openjdk.java.net](https://openjdk.java.net) )  


This post describes how to initialize a variable at the same time as declaring that.  

<!--more-->

# Array

~~~java
public class Sample {

    private final String[] values0 = new String[]{ "1", "2", "3" };

    // only available when declaring an array
    private final String[] values1 = { "1", "2", "3" };
}
~~~

# List

## Mutable List

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

    // available in JDK 5 or later
    private final List<String> values2 = new ArrayList<>(Arrays.asList("1", "2", "3"));
}
~~~

## Immutable List

Follows are the how to make immutable List.  
If something attempts to change an immutable List, UnsupportedOperationException will be thrown.  

~~~java
public class Sample {

    private final List<String> values0 = Arrays.asList(new String[]{ "1", "2", "3" });

    private final List<String> values1 = Collections.unmodifiableList(new ArrayList<>(values0));

    // available in JDK 5 or later
    private final List<String> values2 = Arrays.asList("1", "2", "3");

    // available in JDK 9 or later
    private final List<String> values3 = List.of("1", "2", "3");
}
~~~

# Set

## Mutable Set

~~~java
public class Sample {
    private final Set<String> values = new HashSet<>(Arrays.asList("1", "2", "3"));
}
~~~

## Immutable Set

Follows are the how to make immutable Set.  
If something attempts to change an immutable Set, UnsupportedOperationException will be thrown.  


~~~java
public class Sample {

    private final Set<String> values0 = Collections.unmodifiableSet(new HashSet<String>(Arrays.asList("1", "2", "3")));

    // available in JDK 9 or later
    private final Set<String> values1 = Set.of("1", "2", "3");
}
~~~

# Map

## Mutable Map

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

Follows are the how to make immutable Map.  
If something attempts to change an immutable Map, UnsupportedOperationException will be thrown.  

Collections is a utility class for the Collection interface.  
Although the Map interface does not extend the Collection interface, some feature of Collections class support Collection iterface.  

~~~java
public class Sample {
    private final Map<String, String> map0 = Collections.unmodifiableMap(new HashMap<String, String>() {
        {
            put("key01", "val01");
            put("key02", "val02");
            put("key03", "val03");
        }
    });

    // available in JDK 9 or later
    // only 10 keys can be available
    Map<String, String> map1 = Map.of("key01","value01", "key02", "value02");
}
~~~


# Empty Immutable Collection

Follows are the how to make empty immutable List, Set, and Map.  
These empty immutable things are very efficient from resources standpoint because only one exists in the process.  

If something attempts to change these, UnsupportedOperationException will be thrown.  

~~~java
public class Sample {

    // available in JDK 5 or later
    private final List<String> emptyList0 = Collections.emptyList();
    private final Set<String> emptySet0 = Collections.emptySet();
    private final Map<String, String> emptyMap0 = Collections.emptyMap();

    // available in JDK 9 or later
    private final List<String> emptyList1 = List.of();
    private final Set<String> emptySet1 = Set.of();
    private final Map<String, String> emptyMap1 = Map.of();
}
~~~







