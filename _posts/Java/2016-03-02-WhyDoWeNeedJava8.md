---
layout: post
title:  "왜 Java 8 을 공부해야 하는가?"
date:   2016-03-02 12:00:00 
categories: Java
tags: Stream Java8
---

![http://eclipsesource.com/](http://eclipsesource.com/blogs/wp-content/uploads/2014/03/java8_logo-281x300.png)  

( 출처 : [http://eclipsesource.com/](http://eclipsesource.com/blogs/2014/03/24/how-to-use-swt-with-java-8/) )

Java 8 이 새로 나오고 인터넷의 다양한 매체로부터 Lambda 라는 용어를 수없이 많이 들었습니다.  

그 때문이었을까요.. Java 8 의 변화는 Lambda 라는 잘못된 인식을 가졌고  
Java 8에 대해서 자세히 들여다보지 않는 실수를 저질렀습니다. 지금이라도 관심을 가진건 다행이죠.  

Java 8 의 가장 큰 변화는 Stream 일 것입니다.  
Stream 을 이용하면 멀티코어 프로세서상에서 병렬처리가 너무나도 쉽고 정확하게 이뤄집니다.  
개발자의 잠재적 실수가 숨어있을 공간이 거의 사라진 것처럼 느껴질 정도네요.  

이 문서에서는 Java 8 사용 권유의 목적으로 Stream 에 대해서 간략히 설명하도록 하겠습니다.  

<!--more-->

# Stream  

> A sequence of elements from a source that supports aggregate operations.  
>
>  - Sequence of elements: A stream provides an interface to a sequenced set of values of a specific element type. However, streams don’t actually store elements; they are computed on demand.  
  - Source: Streams consume from a data-providing source such as collections, arrays, or I/O resources.  
  - Aggregate operations: Streams support SQL-like operations and common operations from functional programing languages, such as filter, map, reduce, find, match, sorted, and so on.   
>
> ( 출처 : [oracle.com/java-se-8-streams](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html) )

Stream 은 하나의 source로부터 제공받은 요소들의 연속체입니다. 그리고 stream은 집계 연산을 지원합니다.  
  
  * Stream 은 요소들의 연속체에 대한 인터페이스를 제공합니다. 하지만 요소들을 실제로 보관하지 않고 계산하는데만 사용합니다.  
  * Stream 이 소모하는 자료는 collections, arrays 등으로부터 제공됩니다.  
  * Stream 은 SQL 과 함수형 프로그래밍 언어의 집계 연산을 제공한다. 예를들어 filter, map, reduce, find, match, sorted, forEach 등이 있습니다.  

또한 Stream 은 병렬 처리도 가능합니다.


## Before & After Code Style

아래는 Java 7 이전버전에서  Collection API를 이용해서 작성된 코드입니다.  
코드의 목적은 transactions 중에서 GROCERY 만 필터링하고 value로 정렬 후 ID 리스트를 얻는 것입니다.  

코드의 출처는 [oracle.com/java-se-8-streams](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html) 입니다.  

~~~java
// Java 7 이전 스타일
List<Transaction> transactions = getTransactions();
List<Transaction> groceryTransactions = new Arraylist<>();

// filter
for(Transaction t: transactions){
  if(t.getType() == Transaction.GROCERY){
    groceryTransactions.add(t);
  }
}

// sort
Collections.sort(groceryTransactions, new Comparator(){
  public int compare(Transaction t1, Transaction t2){
    return t2.getValue().compareTo(t1.getValue());
  }
});

// map, collect
List<Integer> transactionIds = new ArrayList<>();
for(Transaction t: groceryTransactions){
  transactionsIds.add(Integer.parseInt(t.getId()));
}
~~~

제 기준에서 봤을 때는 잘 짜여진 코드로 보여집니다.  
하지만 Java 8 샘플을 보시고 나면 생각이 달라지실 겁니다.  

Lambda 도 사용되었기 때문에 정확한 이해가 어려울 수 있지만 일단 가볍게 넘어가시기 바랍니다.  

~~~java
// Java 8
List<Transaction> transactions = getTransactions();
List<Integer> transactionsIds = 
    transactions.stream()
                .filter(t -> t.getType() == Transaction.GROCERY)
                .sorted(comparing(Transaction::getValue).reversed())
                .mapToInt(Transaction::getId)
                .collect(toList());
~~~

Java 7 이전 프로그래밍 스타일에 비해서 굉장히 짧습니다.  

그냥 짧다고 좋은 것은 아니죠.  

쓸데없는 코드들이 사라졌습니다.  
Java 7에서는 필터링과 정렬을 위한 groceryTransactions 임시 리스트가 존재하지만 8 에서는 없습니다.  
또한 목적이 두리뭉실한 for, if 문들이 없어졌습니다.  
filter, sort, map, collect 와 같이 명확한 표현만 남았다고 보시면 됩니다.  

다만, 좀 난감해보이는 점이있다면  
기존 Java 에서 볼 수 없었던 문법이 많이 포함되어있습니다.  
이것은 Lambda 와 default method 에 대한 설명이 필요합니다.  

## Parallel
  
transaction 의 개수가 엄청나게 많다고 가정해보겠습니다.  
병렬처리를 하게 되면 CPU core가 N개일 때, N개의 core에게 모두 일을 시킬 수 있습니다.  
병렬처리를 위한 방법 중 하나는 멀티 쓰레드로 처리하도록 만들면 됩니다.  

멀티 쓰레드로 처리하도록 직접 작성하게 된다면,  
멀티 쓰레드를 위한 부수적인 코드가 더 많은 영역을 차지하게 될 것입니다.  
또한 멀티 쓰레드를 위한 코드가 재활용이 가능하냐에 대한 문제도 남습니다.  
게다가 thread safe 한 코드인지 확신하기도 힘들죠.  

만약 Java 8 으로 진행한다면 아래와 같이 간단합니다.  

~~~java
List<Transaction> transactions = getTransactions();
List<Integer> transactionsIds = 
    transactions.parallelStream() // 또는 .stream().parallel()
                .filter(t -> t.getType() == Transaction.GROCERY)
                .sorted(comparing(Transaction::getValue).reversed())
                .mapToInt(Transaction::getId)
                .collect(toList());
~~~

``` .parallel() / .parallelStream() ``` 만 추가해주시면 thread safe 한 병렬처리가 진행됩니다.  

병렬처리가 너무나도 간단하고  
여전히 filter, sort, map, collect 라는 핵심내용이 코드의 대부분을 차지합니다.  
병렬처리를 했음에도 불구하고 코드의 가독성이 여전히 좋습니다.  
게다가 개발자의 실수로 thread safe 하지 못한 코드가 만들어질 경우가 거의 없어보입니다.  

### 주의사항

병렬처리가 항상 좋은 것은 아닙니다.  
개수가 많지 않다면 병렬처리가 오히려 더 성능이 좋지 않을 수 있습니다.  

# 결론

저는 다른 것보다 parallel 때문에라도 Stream 을 무조건 익혀야겠다는 생각을 했습니다.  
여러분은 어떠신가요?  

그리고 Java 8 을 공부하기 직전에  
멀티 쓰레드를 이용해서 작성한 프로그램이 있었습니다.  
근데 Stream 으로 다시 작성하고나서 기존 버전은 바로 폐기처분했답니다.  
프로그램 이름은 [Chopsticks](https://github.com/dveamer/Chopsticks) 이고 아직 미완성입니다.  

Stream 을 이해하기 위해서는  
Lambda, default method, lazy style 등에 대한 이해가 필요합니다.  
그에 대해서는 추후에 다시 글을 작성하며 정리해보겠습니다.

# Reference
  * [docs.oracle.com/Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)
  * [oracle.com/java-se-8-streams](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html)
