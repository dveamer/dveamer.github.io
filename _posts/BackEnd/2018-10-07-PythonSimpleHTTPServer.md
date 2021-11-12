---
layout: post
title: "Python SimpleHTTPServer"
date: 2018-10-07 00:00:00
lastmod: 2021-11-12 00:00:00
categories: BackEnd
tags: BackEnd Python
---

![Python](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/260px-Python_logo_and_wordmark.svg.png){:class="imgTitle"}    
( 이미지 출처 : [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)) )  

Python 명령어 한줄로 간단한 HTTP 서버를 띄우는 방법을 알아봅니다.  

<!--more-->

너무나도 단순한 방법인데 의외로 모르시는 분들이 많아서 공유합니다.  
( 그리고 Python2 와 Python3가 방법이 다르다보니 저도 매번 검색하게되서 외우기 위해 기록합니다. )  

다른 것을 테스트하다가 간단하게 연결해볼 HTTP 서버가 필요하다 싶을 때 사용하면 좋습니다.  
Linux의 경우 Python이 기본적으로 설치되어있기 때문에 다른 준비작업이 필요 없습니다.  

## Python 2.x

~~~terminal
$ python2 -m SimpleHTTPServer 8000
~~~

8000 포트로 간단한 HTTP 서버가 띄워집니다.  
```http://localhost:8000```에 접속해보면 명령을 실행한 디렉토리의 파일내역이 출력됩니다.  

## Python 3.x

~~~terminal
$ python3 -m http.server 8000
~~~

Python 2.x 와 동일한 결과를 출력합니다.  


## SSL 적용


위에서 띄운 Simple HTTP Server을 이용해서 SSL 서버를 띄우는 방법 입니다.  

### key.pem, cert.pem 파일 생성

SSL이 필요로하는 key.pem, cert.pem 파일을 만들기 위해 아래 명령어를 수행하빈다.  

~~~bash
$ openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365
~~~

아래와 같이 적당히 긴 pass phrase를 2회 입력해주셔야 합니다. pass phrase는 추후에 사용되니 반드시 기억해두셔야 합니다.  

~~~bash
Generating a 2048 bit RSA private key
....................+++
.................................+++
writing new private key to 'key.pem'
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
~~~


그 뒤에 인증서 정보 입력 과정이 있는데 엔터만 치시면 기본값으로 설정됩니다.  

~~~bash
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:
Email Address []:
~~~


### Python 2.x


**ssl_simple_http_server.py** 라는 파일을 만들고 아래와 같이 작성 합니다.  

~~~python
import BaseHTTPServer, SimpleHTTPServer
import ssl


httpd = BaseHTTPServer.HTTPServer(('localhost', 4443), SimpleHTTPServer.SimpleHTTPRequestHandler)

httpd.socket = ssl.wrap_socket(httpd.socket,
                               server_side=True,
                               keyfile='path/to/key.pem',
                               certfile='path/to/cert.pem',
                               ssl_version=ssl.PROTOCOL_TLS)

httpd.serve_forever()
~~~

**keyfile**, **certifile** 에는 위에서 생성했던 key.pem, cert.pem 파일의 위치를 적어주시면 됩니다.  


~~~bash
$ python ssl_simple_http_server.py --host=0.0.0.0
~~~


4443 포트로 간단한 HTTPS 서버가 띄워집니다.  
```http://localhost:4443```에 접속해보면 명령을 실행한 디렉토리의 파일내역 SSL이 적용된 채로 출력됩니다.  


### Python 3.x

Python 2.X 와 모든 과정, 결과가 동일하고 **ssl_simple_http_server.py** 파일만 아래 내용으로 교체해주시면 됩니다.  

~~~python
import http.server, ssl

httpd = http.server.HTTPServer(('localhost', 4443), http.server.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket(httpd.socket,
                               server_side=True,
                               keyfile='path/to/key.pem',
                               certfile='path/to/cert.pem',
                               ssl_version=ssl.PROTOCOL_TLS)
httpd.serve_forever()
~~~

#### References 

  * [Python 2 SimpleHttpServer](https://docs.python.org/2/library/simplehttpserver.html)
  * [Python 3 http server](https://docs.python.org/3/library/http.server.html)

