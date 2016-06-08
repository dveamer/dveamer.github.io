---
layout: post
title:  "[Hackerank Challenges] Xor-sequence"
date:   2016-06-07 12:00:00
categories: Programming
tags: Python Algorithm Hackerank
---

![Hackerrank Logo](https://d3keuzeb2crhkn.cloudfront.net/hackerrank/assets/brand/wordmark_for_work_sm-9a35b2885d5592f8878bcefc673f1221.png)  
( 이미지 출처 : [https://www.hackerrank.com/](https://www.hackerrank.com/) )  

지인의 권유로 [Xor-sequence](https://www.hackerrank.com/challenges/xor-se) 라는 문제를 풀어봤다.  

1개의 테스트 케이스에 대해서 O(1)의 성능을 보이도록 프록르매이했으므로  
아마도 "최선의 답일 것이다" 라고 생각하며 뿌듯해하고 있었다.  

문제를 다 풀고 나니까 다른 사람의 풀이도 볼 수 있게되서 둘러보고 있었다.  
그 중 Python 으로 푼 어떤 스위스 사람의 풀이를 열어보고서는 나의 뿌듯함은 연기가 되어 사라졌다.  

<!--more-->

내가 놓친 몇가지 부분들이 눈에 밟힌다.  

그래도 다행(?)인 것은  
스위스 사람빼고 다른 이들의 문제풀이를 열어보면  
읽어보기도 싫을 정도의 코드들도 있다는 점이다.  


자야될 시간이라서 자세히 기록하진 못하고  
나의 풀이와 그의 풀이를 기록해두고 나중에 알고리즘 문제에 대해서 설명을 해보자.  

# 문제설명

# 풀이 

## 핵심내용
  * XOR 연산의 특징
  * 주어진 데이터의 패턴

## 나의 풀이

~~~python
#!/bin/python

import sys

def __sigma_xor(start_basecamp, destination):
    if start_basecamp > destination:
        return 0

    if start_basecamp == destination:
        return start_basecamp

    value = start_basecamp
    result = start_basecamp
    for val in range(start_basecamp+1, destination+1):
        value = value ^ val
        result = result ^ value

    return result


def question(L, R):
    first_basecamp = (L // 8) * 8
    next_basecamp = first_basecamp + 8

    if next_basecamp >= R:
        result = __sigma_xor(first_basecamp, L-1) ^ __sigma_xor(first_basecamp, R)
        return result

    result = __sigma_xor(first_basecamp, L-1) ^ __sigma_xor(first_basecamp, next_basecamp-1)

    last_basecamp = (R // 8) * 8

    result = result ^ __sigma_xor(last_basecamp, R)
    return result

Q = int(input().strip())
for a0 in range(Q):
    L,R = input().strip().split(' ')
    L,R = [int(L),int(R)]
    print(question(L,R))
~~~


## 스위스 사람의 풀이 

~~~python
#!/bin/python

import sys

def f(n):
    r = (n % 8) / 2
    if r == 0:
        return n
    elif r == 1:
        return 2
    elif r == 2:
        return n + 2
    else:
        return 0

Q = int(raw_input().strip())
for a0 in xrange(Q):
    L,R = raw_input().strip().split(' ')
    L,R = [long(L), long(R)]
    print f(R) ^ f(L - 1)
~~~


# 반성 
