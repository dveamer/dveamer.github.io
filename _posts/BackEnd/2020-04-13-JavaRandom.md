---
layout: post
title: "Java Random - ThreadLocalRandom, SplittableRandom, SecureRandom"
date: 2020-04-13 00:00:00
categories: BackEnd
tags: BackEnd Java
---

![https://openjdk.java.net](https://openjdk.java.net/images/openjdk.png){:class="imgTitle"}  
( 이미지 출처 : [https://openjdk.java.net](https://openjdk.java.net) )  


Java에서 제공하는 Random 라이브러리에 대해서 알아봅니다.  

<!--more-->


## 잘못된 사용법

100 보다 작은 무작위 수를 구하는 로직이 필요하다고 가정합시다. 그래서 아래와 같이 특정 bound 값보다 작은 무작위 수를 제공하는 ```random(int bound)``` 메소드를 작성했습니다.  
아래 코드의 출처는 "Effective Java 3/E 한글판 351 page, 아이템59"로 ```java.util.Random```의 잘못된 사용법입니다.  

~~~Java

    static Random rnd = new Random();

    static int random(int bound) {
        return Math.abs(rnd.nextInt()) % bound;
    }

    public static void main(String[] args) {
        int result = random(100);
    }
~~~

얼핏보기에는 random method는 bound 보다 작은 무작위 값을 제공해줄 것 같습니다.  
물론 한 두번 테스트를 해보면 별 문제 없이 무작위 값을 제공해주는 것을 확인할 수 있습니다.  
하지만 수 차례 반복해서 값의 평균을 내보면 무작위 값이 치우치는 경향을 찾을 수 있습니다.  
심지어 nextInt()가 Integer.MIN_VALUE 를 반환하면 math.abs도 Integer.MIN_VALUE를 반환하기 때문에 결과가 음수가 되버리는 경우도 생깁니다.  

여기서 주의할 점은 ```Random.netxInt()``` method에 버그가 있는 것이 아니라  
우리가 작성한 ```random(int bound)``` method에 버그가 있다는 점입니다.  
그리고 가장 큰 문제는 Random을 다루면서 발생하는 오류는 수차례 반복해야 발견할 수 있기 때문에 문제가 있다는 상황을 발견하기도 힘들고 재현도 힘들다는 점니다.  

# 제대로된 사용법

```Random.netxInt()``` method는 Integer.MIN_VALUE부터 Integer.MAX_VALUE까지의 무작위 값이 필요한 경우에만 사용해야 합니다.  

어떤 bound 값보다 작은 무작위 값을 구하고 싶다면, ```java.util```에서 이미 제공하고 있는 다른 방법을 사용만 하면 됩니다.  

~~~java

    static Random rnd = new Random();

    public static void main(String[] args) {
        int result1 = rnd.nextInt(100);

        // JDK 7 이상에서 사용 가능
        int result2 = ThreadLocalRandom.current().nextInt(100);

        // JDK 8 이상에서 사용 가능
        int result3 = new SplittableRandom().nextInt(100);
    }
~~~


## Random.nextInt(int bound)

[java.util.Random](http://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/Random.html)은 이미 특정 bound 값보다 작은 무작위 수를 제공하는 method를 제공하고 있습니다.  
우리는 단지 아래와 같이 사용만 하면 됩니다. 그러면 아까와는 다르게 치우침 없는 평균값을 구할 수 있고 음수값이 나오는 버그도 없을 것입니다.  

~~~Java

    static Random rnd = new Random();

    public static void main(String[] args) {
        int result = rnd.nextInt(100);
    }
~~~


아래는 ```Random.nextInt(int bound)``` 내부 구현 내용입니다.  
```Math.abs()```와 ```MOD``` 연산자를 이용해서 간단하게 작성한 random() 메소드는 사실 아래와 같이 복잡한 로직이 필요합니다.  

~~~java

    public int nextInt(int bound) {
        if (bound <= 0) {
            throw new IllegalArgumentException("bound must be positive");
        } else {
            int r = this.next(31);
            int m = bound - 1;
            if ((bound & m) == 0) {
                r = (int)((long)bound * (long)r >> 31);
            } else {
                for(int u = r; u - (r = u % bound) + m < 0; u = this.next(31)) {
                }
            }

            return r;
        }
    }
~~~


이미 수많은 사람들이 검증한 library를 사용하지 않고 재작성하면  

```random(int bound)``` method를 작성하는 시간도 들고  
나중에 버그를 발견해서 원인을 찾고 수정하느라 시간도 들게 됩니다.  

물론 ```Random.nextInt(int bound)```이 제공해주는 로직에도 버그가 있을 수 있습니다.  
하지만 혹시 나중에 새로운 버그가 발견된다면 새로운 JDK에서는 버그가 해결되어 제공될 것입니다.  
게다가 성능개선이 이뤄진다면 그 또한 새로운 JDK에서 제공 됩니다.  
우리는 소스 수정없이도 사용하는 JDK의 버전만 올리면 개선된 ```Random.nextInt(int bound)``` method의 혜택을 얻을 수 있습니다.  

뿐만 아니라 다른 동료 개발자 입장에서는 좀 더 낯익고 [https://openjdk.java.net](https://openjdk.java.net)에 문서화된 든든한 코드가 될 것입니다.  

결론은 java.lang, java.util, java.io 에서 제공하는 기능들을 최대한 많이 숙지하고 활용해야한다는 것입니다.  

## ThreadLocalRandom

[java.util.concurrent.ThreadLocalRandom](http://cr.openjdk.java.net/~iris/se/13/spec/latest/api/java.base/java/util/concurrent/ThreadLocalRandom.html)은 thread별로 격리된 random number generator 입니다. Random 보다 더 고품질의 무작위 수를 생성해주고 속도도 빠릅니다.  

~~~Java
    public static void main(String[] args) {
        // JDK 7 이상에서 사용 가능
        int result2 = ThreadLocalRandom.current().nextInt(100);
    }
~~~

참고로 Random 도 thread-safe하고 ThreadLocalRandom도 thread-safe합니다. 하지만 그 이유는 다릅니다. Random은 seed를 AutomicLong을 사용하기 때문에 멀티 쓰레드의 요청에 대해서도 순서대로 처리하여 마치 동기화(synchronized) 처리처럼 동작하기 때문에 느립니다. ThreadLocalRandom은 AutomicLong 사용하지 않고 thread별로 seed값을 다르게 관리하기 때문에 Random보다 멀티 쓰레드에 대해 응답이 빠르면서도 thread-safe 합니다.  

결론을 이야기 하자면, JDK 7부터는 Random을 사용할 이유가 없습니다. ThreadLocalRandom을 쓰면 됩니다.  

## SplittableRandom

[java.util.SplittableRandom](http://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/util/SplittableRandom.html)는 격리된 병렬처리에 특화된 random number generator 입니다.  

~~~java
    public static void main(String[] args) {
        // JDK 8 이상에서 사용 가능
        int result3 = new SplittableRandom().nextInt(100);
    }
~~~

SplittableRandom은 thread safe하지 않습니다. 잘못 사용하면 문제가 발생하기 때문에 주의해야 합니다.  
예를들어, for/join-style 처리시 매번 split() 메소드를 이용해서 SplittableRandom 인스턴스를 재 생성해야합니다. 만약에 Random을 사용하듯이 하나의 SplittableRandom을 여러 쓰레드에서 사용하게 되면 무작위수의 품질이 떨어질 수 있습니다. 또한 split() 메소드를 사용하지 않고 생성자 호출방식으로 매번 생성한다면 동일한 시각에 생성된 SplittableRandom의 경우는 seed가 동일해서 매번 같은 값을 출력하게 됩니다.  

split() 메서드는 새로운 SplittableRandom 인스턴스를 생성해서 제공하는데 두 인스턴스는 서로 공유하는 mutable state가 없습니다. 게다가 split()을 통해 반복적으로 생성된 인스턴스들이 만들어낸 무작위 수들의 품질은 SplittableRandom 인스턴스 하나에서 만들어낸 무작위 수들의 품질과 동일합니다.  

결론적으로 포크조인풀이나 병렬스트림 처리를 할 때는 ThreadLocalRandom 보다도 SplittableRandom을 사용하는 것이 품질적인 측면에서 더 좋습니다. 단, 사용법에 주의해야합니다.  


## SecureRandom

[java.security.SecureRandom](http://cr.openjdk.java.net/~iris/se/12/latestSpec/api/java.base/java/security/SecureRandom.html)는 무작위수를 생성하기 위한 알고리즘을 선택할 수 있습니다. 그래서 Random, ThreadLocalRandom, SplittableRandom 보다 훨씬 우월한 품질의 무작위 수를 만들어 낼 수 있습니다. 보안이 중요한 업무에서는 SecureRandom을 사용해야 합니다.  

예를들어, Random의 경우는 [Linear Congruential Generator (LCG)](https://en.wikipedia.org/wiki/Linear_congruential_generator) 알고리즘을 사용하는데 이 알고리즘은 결과의 패턴이 있기 때문에 보안이 중요한 일을 위해 무작위 수를 만드는 경우에는 적합하지 않습니다. SecureRandom은 [cryptographically strong pseudo-random number generator (CSPRNG)](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator) 알고리즘을 사용합니다. 품질 차이는 [The Java SecureRandom Class](https://www.baeldung.com/java-secure-random) 페이지에서 이미지로 확인 가능합니다.  

SecureRandom가 제공가능한 CSRPNG 알고리즘은 NativePRNG, NativePRNGBlocking, NativePRNGNonBlocking, PKCS11, SHA1PRNG, Windows-PRNG 이 있습니다. - [참고](https://docs.oracle.com/javase/8/docs/technotes/guides/security/StandardNames.html#SecureRandom)

그 중 SecureRandom의 기본 알고리즘은 SHA1PRNG 입니다. 그리고 아래와 같이 다른 알고리즘을 선택 가능합니다.  

~~~Java
    SecureRandom secureRandom = SecureRandom.getInstance("NativePRNG");
~~~

JDK 1.1 부터 제공되었으며 thread safe 합니다.  
