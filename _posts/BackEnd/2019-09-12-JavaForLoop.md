---
layout: post
title: "Java For-Loop : Array, List, Set, Map"
date: 2019-09-12 00:00:00
categories: BackEnd
tags: BackEnd Java
---

Array, List, Set, Map 에 대한 for-loop 방법을 기술합니다.  

<!--more-->

# Array

~~~java

int[] values = { 3, 5, 7, 23, 1, 2, 4 };

// for loop by index
for(int i=0; i<values.length; i++){
    System.out.println(values[i]);
}

// for loop
// JDK 5 이상에서 사용 가능
for(int v : values){
    System.out.println(v);
}

// stream forEach
// JDK 8 이상에서 사용 가능
Arrays.stream(values)
        .filter(v -> v<10) // 10 이상의 숫자는 필터링
        .forEach(v-> System.out.println(v));
~~~


# List 

~~~java
List<Integer> values = Arrays.asList(3, 5, 7, 23, 1, 2, 4 );

// for loop by index
for(int i=0; i<values.size(); i++){
    System.out.println(values.get(i));
}

// iterator
Iterator<Integer> iterator = values.iterator();
while(iterator.hasNext()){
    System.out.print(iterator.next());
}

// for loop
// JDK 5 이상에서 사용 가능
for(Integer v : values){
    System.out.println(v);
}

// collection forEach
// JDK 8 이상에서 사용 가능
values.forEach(v-> System.out.println(v));

// stream forEach
// JDK 8 이상에서 사용 가능
values.stream()
        .filter(v -> v<10) // 10 이상의 숫자는 필터링
        .forEach(v-> System.out.println(v));
~~~


# Collection

List, Set, Queue와 같은 Collection 객체들은 iterator, for-loop, collection forEach, stream ForEach가 모두 가능합니다.  

~~~java
...(생략)

public class ForLoopSample {

    public static void main(String[] args) {
        // list
        loopCollection(Arrays.asList(3, 5, 7, 23, 1, 2, 4 ));

        // set
        loopCollection(new HashSet<>(Arrays.asList(3, 5, 7, 23, 1, 2, 4 )));

        // queue
        Queue<Integer> queue = new LinkedList<>(Arrays.asList(3, 5, 7, 23, 1, 2, 4 ));
        loopCollection(queue);
    }

    public static <E> void loopCollection(Collection<E> collection){

        // iterator
        Iterator<E> iterator = collection.iterator();
        while(iterator.hasNext()){
            System.out.println(iterator.next());
        }

        // for loop
        // JDK 5 이상에서 사용 가능
        for(E v : collection){
            System.out.println(v);
        }

        // collection forEach
        // JDK 8 이상에서 사용 가능
        collection.forEach(v-> System.out.println(v));

        // stream forEach
        // JDK 8 이상에서 사용 가능
        collection.stream()
                .filter(v -> v instanceof Integer ? ((int)v )<10 : false) // 10 이상의 숫자는 필터링
                .forEach(v-> System.out.println(v));
    }

}
~~~


# Map

~~~java
Map<String, Integer> map = new HashMap<>();
map.put("key01", 30);
map.put("key02", 2);
map.put("key03", 1);
map.put("key04", 41);
map.put("key05", 5);
map.put("key06", 12);

// for loop
for(Map.Entry<String, Integer> entry : map.entrySet()){
    System.out.println(entry.getValue());
}

// map forEach
// JDK 8 이상에서 사용 가능
map.forEach((key, value) -> System.out.println(value) );
~~~





 

