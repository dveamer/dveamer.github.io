---
layout: post
title:  "HTTPD (Apache HTTP Server) 설치하기"
date:   2018-02-03 00:00:00
categories: Programming
tags: Setting Apache
---

![Apache HTTP Server](https://c2.staticflickr.com/8/7640/16527980754_76dfa9c86e_b.jpg){:class="imgTitle"}  
(이미지 출처 : [https://www.flickr.com/photos/rbowen](https://www.flickr.com/photos/rbowen/16527980754))  

HTTPD 설치방법을 알아봅니다.  

Apache에서 만든 [HTTP Server Project](https://httpd.apache.org/)의 결과물이 HTTPD 입니다.  
다시 말해서 Apache가 만든 WEB서버 역할을 하는 middleware 입니다.  

정적인 파일 출력성능이 뛰어납니다.  
Load-balancing과 health-check 기능들을 가지고 WAS서버의 HA확보 기능도 제공합니다.  

흔히 Apache라고 불려서 혼란의 원인이 됩니다.  

<!--more-->

Apache HTTP Server 혹은 HTTPD 라고 불러야 하지 않을까 생각합니다.  

하지만 한번에 여러 사람과 일하고 그 멤버가 수시로 바뀌다보니  
"Apache 라고 부르지 맙시다!" 라고 얘기해봤자 모두가 변하기는 쉽지 않습니다.  
결국 저도 습관적으로 Apache라고 부르는 경우가 대부분입니다.  

<br>

직접 컴파일해서 설치하는 방법을 설명합니다. ( 참고 : [Linux에서 컴파일 설치법을 알아야하는 이유](/programming/CompilingInstallationOnLinux.html) )

# Environment

  * RHEL 6.8

# Preparation Tasks

HTTPD가 사용하게 될 외부 프로그램들을 미리 설치해둬야 합니다.  

그리고 빌드를 위한 gcc 프로그램을 필요합니다.  
근데 아마도 gcc, gcc-c++은 설치되어있을 것입니다.  


## Install APR (Apache Portable Runtime)

HTTPD와 APR과의 버전 의존성이 있습니다.  

근데 CentOS, Redhat 에는 구버전의 APR이 설치되어있어서  
최신 Apache HTTP Server를 이용하실거라면 APR 설치가 필요합니다.  

  * 버전확인 : [https://apr.apache.org/download.cgi](https://apr.apache.org/download.cgi) 

### APR

~~~terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget http://mirror.navercorp.com/apache/apr/apr-1.6.3.tar.gz
$ tar zxvf apr-1.6.3.tar.gz
$ cd apr-1.6.3

// compile
$ ./configure --prefix=/usr/local/apr
$ make
$ make install
~~~

### APR-util

~~~terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget http://mirror.navercorp.com/apache/apr/apr-util-1.6.1.tar.gz
$ tar zxvf apr-util-1.6.1.tar.gz
$ cd apr-util-1.6.1

// compile
$ ./configure --prefix=/usr/local/apr-util
$ make
$ make install
~~~


#### References

  * [https://apr.apache.org](https://apr.apache.org)

## Install PCRE (Perl Compatible Regular Expressions)

  * 버전확인 : [https://ftp.pcre.org/pub/pcre/](https://ftp.pcre.org/pub/pcre/)

~~~terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget https://ftp.pcre.org/pub/pcre/pcre-8.41.tar.gz
$ tar zxvf pcre-8.41.tar.gz
$ cd pcre-8.41

// compile
$ ./configure --prefix=/usr/local/pcre
$ make
$ make install
~~~

#### Reference

  * [https://www.pcre.org/](https://www.pcre.org/)

## Install Openssl

  * 버전확인 : [https://www.openssl.org/source/](https://www.openssl.org/source/)

~~~terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget https://www.openssl.org/source/openssl-1.0.2n.tar.gz
$ tar zxvf openssl-1.0.2n
$ cd openssl-1.0.2n

// compile
$ ./config -fPIC --prefix=/usr/local/ssl
$ make depend
$ make
$ make install
~~~

#### References

  * [https://www.openssl.org/](https://www.openssl.org/)


## Install nghttp2

http/2.x 를 이용하기 위한 프로그램입니다.  

  * 버전확인 : [https://github.com/nghttp2/nghttp2/releases/download/](https://github.com/nghttp2/nghttp2/releases/download/)

~~~ terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget https://github.com/nghttp2/nghttp2/releases/download/v1.29.0/nghttp2-1.29.0.tar.gz
$ tar xfz nghttp2-1.29.0.tar.gz
$ cd nghttp2-1.29.0

// compile
$ export LDFLAGS="-l/usr/local/ssl/include"
$ export OPENSSL_CFLAGS="-l/usr/local/ssl/include"
$ export OPENSSL_LIBS="-L/usr/local/ssl -lssl -lcrypto"
$ ./configure --prefix=/usr/local/nghttp2
$ make
$ make install
~~~

#### References

  * [https://github.com/nghttp2](https://github.com/nghttp2)

# Setting HTTPD Apache

## Install Apache HTTP Server

  * 버전확인 : [https://httpd.apache.org/download.cgi](https://httpd.apache.org/download.cgi)

~~~terminal
// download
$ mkdir -p /tmp/install_apache
$ cd /tmp/install_apache
$ wget http://mirror.navercorp.com/apache/httpd/httpd-2.4.29.tar.gz
$ tar xfz httpd-2.4.29.tar.gz
$ cd httpd-2.4.29

// compile
$ ./configure \
--prefix=/user/local/httpd2 \
--enable-unique-id --enable-so --enable-proxy --enable-mods-shared=all \
--with-mpm=worker --enable-mpms-shared-all \
--with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util \
--with-pcre=/usr/local/pcre \
--with-ssl=/usr/local/ssl --enable-ssl \
--with-nghttp2=/usr/local/nghttp2 --enable-http2 --enable-proxy-http2 \
$ make
$ make install 
~~~

#### References

  * [https://httpd.apache.org](https://httpd.apache.org)

## Make An Account 

가장 어려운 과정입니다.  
HTTPD를 관리할 {account}명과 {group}명을 정하세요.  

그런 다음 {account}, {group} 을 생성하세요.  

~~~terminal
$ groupadd {group}
$ useradd -g {group} {account}
$ passwd {account}
~~~

## Establish Authority

~~~terminal
$ chmod 700 /user/local/httpd2 -R
$ chown {account}:{group} /user/local/httpd2 -R
~~~


## Start / Stop HTTPD

### Start HTTPD

~~~terminal
$ /user/local/httpd2/bin/apachectl 
~~~

HTTPD를 ```/user/local/httpd2/```에 설치했기 때문에  
현재 설정파일(httpd.conf)는 ```/user/local/httpd2/conf/httpd.conf```에 위치해있습니다.  

만약 다른 위치의 설정파일을 이용해서 기동하고 싶다면 ```-f``` 옵션을 이용하면 됩니다.  

~~~terminal
$ /user/local/httpd2/bin/apachectl -f /another_path/httpd.conf
~~~

### Stop HTTPD

~~~terminal
$ /user/local/httpd2/bin/apachectl -k stop
~~~

> 부모 프로세스는 즉시 모든 자식을 죽인다. 자식을 완전히 죽이는데는 몇 초가 걸릴 수 있다. 그런후 부모가 종료한다. 처리중인 요청은 중단되고, 더 이상 요청을 받지않는다.
> ( 출처 : [https://httpd.apache.org/docs/2.4/ko/stopping.html](https://httpd.apache.org/docs/2.4/ko/stopping.html) )


### Gracefully Restart HTTPD

~~~terminal
$ /user/local/httpd2/bin/apachectl -k graceful
~~~

> 부모 프로세스는 자식들에게 현재 요청을 처리한후 종료하라고 (혹은 현재 아무것도 처리하지 않다면 즉시 종료하라고) 조언한다. 부모는 설정파일을 다시읽고 로그파일도 다시 연다. 자식이 죽을때마다 부모는 죽은 자식대신 새로운 설정 세대에 기초한 자식을 실행하여 즉시 요청을 처리하게 한다.
> ( 출처 : [https://httpd.apache.org/docs/2.4/ko/stopping.html](https://httpd.apache.org/docs/2.4/ko/stopping.html) )

### Restart HTTPD

~~~terminal
$ /user/local/httpd2/bin/apachectl -k restart
~~~

> 부모 프로세스는 모든 자식을 죽이지만 부모는 종료하지 않는다. 부모는 설정파일을 다시읽고 로그파일을 다시 연다. 그리고 새로운 자식들을 만들고 서비스를 계속한다.
> ( 출처 : [https://httpd.apache.org/docs/2.4/ko/stopping.html](https://httpd.apache.org/docs/2.4/ko/stopping.html) )

#### References

  * [https://httpd.apache.org/docs/2.4/invoking.html](https://httpd.apache.org/docs/2.4/invoking.html)
  * [https://httpd.apache.org/docs/2.4/ko/stopping.html](https://httpd.apache.org/docs/2.4/ko/stopping.html)


## Test

기본설정으로 HTTPD를 실행시키면 80 port가 LISTEN 상태일 것입니다.  

~~~terminal
$ netstat -nap | grep httpd
~~~

브라우저 혹은 cURL 을 이용해서 접속해보면 Apache HTTP Server라는 정보가 출력될 것입니다.  

~~~terminal
$ curl -i http://localhost
~~~

## 설치 후 불필요해진 파일 정리작업

설치를 위해서 다운받은 파일들을 제거합니다.  

~~~terminal
$ rm -rf /tmp/install_apache
~~~

## httpd.conf 설정

HTTPD 설정은 다음 글에서 작성하겠습니다.  




