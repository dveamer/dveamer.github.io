---
layout: post
title:  "Process Status 사용법"
date:   2016-01-12 12:00:00 
lastmod: 2018-02-03 00:00:00
categories: Linux
tags: Command Memory
---

~~~
  ps -eo user,pid,ppid,rss,vsz,size,pmem,pcpu,time,comm

  Result>
  USER   PID   PPID  RSS     VSZ      SIZE    %MEM  %CPU  TIME      CMD
  user1  2699  2615  431076  1421496  991748  7.1   0.9   00:07:44  /opt/google/chrome/
  user1  2584  2016  242248  1266588  596076  4.0   0.9   00:08:06  /opt/google/chrome/
  user1  1750  2615  172932  925232   562376  2.8   0.4   00:00:15  /opt/google/chrome/c
  user1  2841  2016  163652  735372   281308  2.7   0.0   00:00:13  /usr/bin/python3 /us
  user2  1099     1  147772  956008   899016  2.4   0.0   00:00:48  /usr/sbin/mysqld
~~~

프로세스별로 메모리, CPU 등의 상태를 파악하는데 유용합니다.  

프로세스별 메모리 값을 합쳐서 전체 메모리 사용량을 구할 수 있습니다.  
하지만 [Shared Memory](https://ko.wikipedia.org/wiki/%EA%B3%B5%EC%9C%A0_%EB%A9%94%EB%AA%A8%EB%A6%AC)를 사용하는 프로세스들이 존재한다면 중복이 발생하므로 실제 값보다 크게 계산 됩니다.  


<!--more-->

Ubuntu의 용어와 명령어를 기준으로 설명합니다.  

# 명령어 팁

## 상위 N개 출력

  * 상위 10개를 추력하기 위해서는 맨 뒤에 ```--sort -rss | head -n 10``` 을 추가합니다.

~~~
  ps -eo user,pid,ppid,rss,vsz,size,pmem,pcpu,time,comm --sort -rss | head -n 10
~~~

## Command 상세하게 출력

  * comm를 cmd로 변경합니다.

~~~
  ps -eo user,pid,ppid,rssize,vsize,size,pmem,pcpu,time,cmd --sort -rss | head -n 10
~~~


# 메모리 출력량

## RSS (alias RSSSIZE)

> resident set size, the non-swapped physical memory that a task has used (in kiloBytes).  
> (출처 : [Ubuntu Manual](http://manpages.ubuntu.com/manpages/hardy/man1/ps.1.html) )   

  * 프로세스가 실제 점유하고 있는 물리적 메모리의 사이즈입니다.  
  * Swap에 사용되는 메모리는 제외합니다.  

## VSZ (alias VSIZE)

> virtual memory size of the process in KiB(1024-byte units). Device mappings are currently excluded; this is subject to change.   
> (출처 : [Ubuntu Manual](http://manpages.ubuntu.com/manpages/hardy/man1/ps.1.html) )   

  * 결론적으로 실제 메모리 사용량을 파악할 시에는 의미있는 값이 아닙니다.  
  * 변할 수 있는 값이라는 것으로 봐선 실제 메모리 사용량의 limit 으로 볼 수도 없습니다.  

  * RSS 는 킬로바이트인데, VSZ는 킬비바이트로 출력됩니다. 단위를 다르게 출력한 이유는 모르겠습니다.  

### Virtual Memory

> 가상 메모리는 메모리를 관리하는 방법의 하나로, 각 프로그램에 실제 메모리 주소가 아닌 가상의 메모리 주소를 주는 방식을 말한다.  
> 이러한 방식은 멀티태스킹 운영체제에서 흔히 사용되며, 실제 주기억장치보다 큰 메모리 영역을 제공하는 방법으로도 사용된다. 가상적으로 주어진 주소를 가상 주소(virtual address) 또는 논리 주소(logical address) 라고 하며, 실제 메모리 상에서 유효한 주소를 물리 주소(physical address) 또는 실주소(real address)라고 한다. 가상 주소의 범위를 가상 주소 공간, 물리 주소의 범위를 물리 주소 공간이라고 한다.  
> 가상 주소 공간은 메모리 관리 장치(MMU) 에 의해서 물리 주소로 변환된다.  

이 덕분에 프로그래머는 가상 주소 공간상에서 프로그램을 짜게 되어 프로그램이나 데이터가 주메모리상에 어떻게 존재하는지를 의식할 필요가 없어진다.  

> 대부분의 현대적 아키텍처와 운영체제는 가상 메모리 기능을 제공하며, 각각의 응용프로그램에 더 적합한 메모리 관리를 위해 어도비 포토샵과 같은 일부 응용 프로그램은 스스로 가상 메모리를 관리하기도 한다.  
> (출처 : [위키피디아](http://ko.wikipedia.org/wiki/%EA%B0%80%EC%83%81_%EB%A9%94%EB%AA%A8%EB%A6%AC) )  

## SIZE
> approximate amount of swap space that would be required if the process were to dirty all writable pages and then be swapped out. This number is very rough!  
> (출처 : [Ubuntu Manual](http://manpages.ubuntu.com/manpages/hardy/man1/ps.1.html) )  

  * Swap 을 위한 메모리 공간입니다.  
  * 이 메모리 량은 굉장히 대략적인 수치입니다.  

#### References

  * [Ubuntu Manual : PS](http://manpages.ubuntu.com/manpages/hardy/man1/ps.1.html)  
  * [Java Process의 가상 메모리와 실제 메모리의 차이](http://novathin.kr/24)  
  * [위키피디아](http://ko.wikipedia.org/wiki/%EA%B0%80%EC%83%81_%EB%A9%94%EB%AA%A8%EB%A6%AC)  
  * [리눅스 메모리 사용량순 프로세스 보기](http://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%A9%94%EB%AA%A8%EB%A6%AC_%EC%82%AC%EC%9A%A9%EB%9F%89%EC%88%9C_%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4_%EB%B3%B4%EA%B8%B0)  
  * [리눅스 메모리 사용률 확인](http://zetawiki.com/wiki/%EB%A6%AC%EB%88%85%EC%8A%A4_%EB%A9%94%EB%AA%A8%EB%A6%AC_%EC%82%AC%EC%9A%A9%EB%A5%A0_%ED%99%95%EC%9D%B8)  
  * [Shared Memory](https://ko.wikipedia.org/wiki/%EA%B3%B5%EC%9C%A0_%EB%A9%94%EB%AA%A8%EB%A6%AC)  
 

