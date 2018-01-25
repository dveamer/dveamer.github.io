---
layout: post
title:  "Apache HTTP Server 설치하기"
date:   2018-01-27 00:00:00
categories: Programming
tags: Setting Apache
---

Apache HTTP Server 설치방법을 알아봅니다.

<!--more-->

# Environment

  * RHEL 6.8

# Preparation Tasks

Apache가 사용하게 될 외부 프로그램들을 미리 설치해둬야 합니다.  
그리고 빌드를 위한 gcc 프로그램을 설치합니다.  

## Install gcc package

~~~terminal
// On Ubuntu
$ sudo apt-get install gcc g++

// On CentOS
$ yum install gcc gcc-c++
~~~

## Install pcre

~~~terminal
// On Ubuntu
$ sudo apt-get install libpcre3 libpcre3-dev

// On CentOS
$ yum install pcre
~~~

## Install Openssl

~~~terminal
$ sudo apt-get openssl
~~~

## nghttp2

~~~ terminal
$ mkdir -p /tmp/nghttp2
$ cd /tmp/nghttp2
$ wget https://github.com/nghttp2/nghttp2/releases/download/v1.29.0/nghttp2-1.29.0.tar.gz
$ tar xfz nghttp2-1.29.0.tar.gz
$ cd nghttp2-1.29.0
$ export LDFLAGS='-l/usr/local/ssl/include"
$ export OPENSSL_CFLAGS="-l/usr/local/ssl/include"
$ export OPENSSL_LIBS="-L/usr/local/ssl -lssl -lcrypto"
$ ./configure --prefix=/usr/local/nghttp2
$ make
$ make install
~~~

http2를 이용하기 위한 프로그램입니다.   
[nghttp2 releases](https://github.com/nghttp2/nghttp2/releases) 에 가셔서 신규버전 확인 후 해당 버전에 맞게 설치하세요.

### 설치 위치 정하기

nghttp2 의 설치 위치를 직접 정하고 싶으시면 ```configure``` 실행시에 path를 파라미터로 입력해주면 됩니다.  

~~~terminal
$ ./configure --prefix=/usr/local/nghttp2
~~~

# Setting Apache

Tomcat과 같이 JVM 위에서 돌아가는 프로그램들은 압축만 풀어서 사용하면 되지만  
Apache는 C언어로 짜여진 프로그램이기 때문에 컴파일 설치가 필요합니다.  
OS 종류, 버전이 달라지면 새롭게 컴파일이 필요하므로 서버 이관 작업, scale-out 시에 고려해야할 사항입니다.  

Ubuntu에서는  ```apt-get install```로 설치가 가능하지만 그 방법도 내부적으로는 컴파일이 진행될 것 입니다.  

저는 직접 컴파일해서 설치, 운영해본 경험밖에 없는 관계로 컴파일을 통한 방법을 다루겠습니다.  


## Install Apache

~~~terminal
$ ./configure --prefix=/user/local/apache2 --enable-unique-id --enable-so --enable-ssl \
--with-mpm=worker --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util \
--with-pcre=/usr/local/pcre --enable-mpms-shared-all \
--enable-http2 --with-nghttp2=/usr/local/nghttp2 --with-ssl=/usr/local/ssl \
--enable-proxy --enable-proxy-http2 --enable-mods-shared=all
$ make
$ make install 
~~~


## Make An Account 

~~~terminal
$ groupadd web_group
$ useradd -g web_group apache
$ passwd apache
~~~

## Establish Authority

~~~terminal
$ chmod 700 /user/local/apache2
$ chown apache:web_group /user/local/apache2
~~~





# Tomcat Shutdown 실패

# Tomcat jvmRoute 설정




