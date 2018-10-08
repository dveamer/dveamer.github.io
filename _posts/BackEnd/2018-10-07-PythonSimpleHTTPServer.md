---
layout: post
title: "Python SimpleHTTPServer"
date: 2018-10-07 00:00:00
categories: BackEnd
tags: BackEnd Python
---

![Python](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/260px-Python_logo_and_wordmark.svg.png)  
( 출처 : [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)) )  

Python 명령어 한줄로 간단한 HTTP 서버를 띄우는 방법을 알아봅니다.  

<!--more-->

너무나도 단순한 방법인데 의외로 모르시는 분들이 많아서 공유합니다.  
( 그리고 Python2 와 Python3가 방법이 다르다보니 저도 매번 검색하게되서 외우기 위해 기록합니다. )  

다른 것을 테스트하다가 간단하게 연결해볼 HTTP 서버가 필요하다 싶을 때 사용하면 좋습니다.  
Linux의 경우 Python이 기본적으로 설치되어있기 때문에 다른 준비작업이 필요 없습니다.  

## Python 2.x

~~~python
$ python2 -m SimpleHTTPServer 8000
~~~

8000 포트로 간단한 HTTP 서버가 띄워집니다.  
```http://localhost:8000```에 접속해보면 명령을 실행한 디렉토리의 파일내역이 출력됩니다.  

## Python 3.x

~~~python
$ python3 -m http.server 8000
~~~

Python 2.x 와 동일한 결과를 출력합니다.  

#### References 

  * [Python 2 SimpleHttpServer](https://docs.python.org/2/library/simplehttpserver.html)
  * [Python 3 http server](https://docs.python.org/3/library/http.server.html)

