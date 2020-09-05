---
layout: post
title:  "How to install Nginx & Tomcat"
date:   2016-12-07 20:00:00
lastmod: 2018-02-03 00:00:00
categories: BackEnd
tags: BackEnd Setting Nginx Apache Tomcat
---

![Nginx Logo](https://cdn.wp.nginx.com/wp-content/uploads/2015/04/NGINX_logo_rgb-01.png){:class="imgTitle"}
![Tomcat Logo](http://tomcat.apache.org/res/images/tomcat.png){:class="imgTitle"}  
(이미지 출처 : [https://www.nginx.com](https://www.nginx.com), [http://tomcat.apache.org/](http://tomcat.apache.org/))  

Nginx와 Tomcat을 이용해서 기본적인 WEB-WAS 환경을 구성해보겠습니다.  

Nginx, Tomcat 설치와 연동에 대해서 알아봅니다.  

<!--more-->

# 구성환경 

  * RHEL 6.8 (Santiago)
  * JDK 1.6

# Apache-Tomcat

Apache사에서 만들었으며 보통 Tomcat 이라고 불립니다.  
J2EE에서 요구하는 WAS서버의 스펙을 대부분 처리가능합니다.  

정적인 파일을 입출력 뿐만 아니라 jsp와 같은 동적인 파일의 구성이나 Java 프로그램 실행이 가능합니다.  
하지만 정적인 파일의 입출력은 WAS보다는 WEB서버가 처리속도가 빠르기 때문에  
주로 Nginx와 같은 WEB서버와 연동해서 사용합니다.  

  * 라이센스 : [Apache License version 2](http://www.apache.org/licenses/)

## Download Apache-Tomcat

설치형식이 아닌 다운로드해서 사용하는 타입입니다.  
저의 경우는 JDK 1.6에 호환되는 Apache-Tomcat 7을 설치했습니다.  

먼저 JDK 버전을 고려해서 필요한 Apache-Tomcat 버전을 확인해야합니다.  
브라우저에서 [http://mirror.apache-kr.org/tomcat](http://mirror.apache-kr.org/tomcat)로 접속하면 필요한 버전별 파일을 찾으실 수 있습니다.  
해당파일의 url을 wget하거나 그외 방법으로 받으셔서 진행하면 됩니다.  

~~~terminal
$ mkdir /usr/local/server
$ cd usr/local/server
$ wget http://mirror.apache-kr.org/tomcat/tomcat-7/v7.0.73/bin/apache-tomcat-7.0.73.tar.gz
$ gunzip apache-tomcat-7.0.73.tar.gz
$ tar -xvf apache-tomcat-7.0.73.tar
$ rm -rf apache-tomcat-7.0.73.tar
$ ln -s apache-tomcat-7.0.73 tomcat
~~~

## Add An Tomcat User

Tomcat 서버를 관리할 계정을 생성합니다.  
root로 관리해도 되지만 보안상의 이유로 추천하지 않습니다.  
Tomcat 서버에 취약점이 있어서 해킹당할시 root권한을 뺏기지 않기 위함입니다.  

~~~terminal
$ groupadd tomcat
$ useradd -g tomcat tomcat
$ passwd tomcat
~~~

## Change File Permissions

Apache-Tomcat을 설치해둔 디렉토리의 파일권한을 tomcat 계정, 그룹에 할당합니다.  
tomcat 계정으로 Apache-Tomcat을 실행 시킬 수 있습니다.  

~~~terminal
$ chown -R tomcat:tomcat /usr/local/server/apache-tomcat-7.0.73
$ chown -R tomcat:tomcat /usr/local/server/tomcat
$ chmod 755 /usr/local/server/tomcat
~~~ 

## Modify /etc/profile 

```/etc/profile```을 수정해서 환경변수를 설정합니다.  
주의할 점은 root뿐만 아니라 모든 계정이 설정된 환경변수를 따르게 됩니다.  
기존에 특정 계정으로 Apache-Tomcat, JDK를 사용중이셨다면 영향을 받을 수 있습니다.  

~~~bash
# vi /etc/profile

...

JAVA_HOME=/usr/local/java
CATALINA_HOME=/usr/local/server/tomcat
CLASSPATH=.:$JAVA_HOME/lib/tools.jar:$CATALINA_HOME/lib-jsp-api.jar:$CATALINA_HOME/lib/servlet-api.jar
PATH=$PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin

export JAVA_HOME CLASSPATH PATH CATALINA_HOME
~~~

수정한 환경변수를 적용하기 위해서 ```/etc/profile```을 실행시킵니다.  
다음부터는 OS를 부팅하면 자동으로 적용됩니다.  

~~~terminal
$ source /etc/profile
~~~

## Startup Apache-Tomcat

tomcat 계정으로 접속해서 실행시킵니다.  

~~~terminal
$ su tomcat
tomcat$ startup.sh
~~~

```curl``` 혹은 브라우저로 ```localhost:8080```에 접속해서 Apache-Tomcat이 실행 된 것을 확인합니다.  

~~~terminal
tomcat$ curl -I localhost:8080
~~~

## Shutdown Apache-Tomcat

tomcat 계정으로 접속해서 중지시킬 수 있습니다.  

~~~terminal
tomcat$ shutdown.sh
~~~

## Automatically Start Apache-Tomcat 

매번 tomcat계정으로 접속해서 실행시킬 필요없이  
OS가 부팅되면 자동으로 Apache-Tomcat이 실행되도록 설정합니다.  

### Make /etc/rc.d/init.d/tomcat

일단 ```/etc/rc.d/init.d/tomcat```파일을 생성합니다.  
해당 파일은 service를 통해서 tomcat을 실행, 중지, 재실행 시키기 위한 shell script입니다.  

~~~bash
# vi /etc/rc.d/init.d/tomcat

#!/bin/bash
# Startp script for the Tomcat Server
# chkconfig: 345 50 50
# description: Tomcat is a Web application server.
. /etc/profile
case "$1" in 
    start)
        echo "Starting tomcat. "
        su - tomcat -c $CATALINA_HOME/bin/startup.sh
    stop)
        echo "Shutting down tomcat: "
        su - tomcat -c $CATALINA_HOME/bin/shutdown.sh

    restart)
        echo "Restarting tomcat: "
        su - tomcat -c $CATALINA_HOME/bin/shutdown.sh
        su - tomcat -c $CATALINA_HOME/bin/startup.sh
    *)
        echo "Usage: service tomcat {start|stop|restart}"
        exit 1
esac
exit 0
~~~

### Change File Permission

```/etc/rc.d/init.d/tomcat```파일의 파일권한을 변경합니다.  

~~~terminal
$ chown tomcat:tomcat /etc/rc.d/init.d/tomcat
$ chomd 755 /etc/rc.d/init.d/tomcat
~~~

### service tomcat {start|stop|restart}

이제부터는 root계정으로 service를 통해서 Apache-Tomcat을 실행, 중지, 재실행 가능합니다.  
root 계정으로 실행시켜도 tomcat 계정의 권한으로 실행됩니다.  

~~~terminal
$ service tomcat start
$ service tomcat restart
$ service tomcat stop
~~~

### Register /etc/rc.d/init.d/tomcat

OS 부팅시에 자동으로 실행되도록 ```/etc/rc.d/init.d/tomcat``` 파일을 등록합니다.  

~~~terminal
$ chkconfig --add tomcat
~~~

## Make Test Page

조금있다가 Nginx와 연동할 때 테스트를 위해 출력할 페이지를 생성해둡니다.  

~~~terminal
$ cd /usr/local/server/tomcat/webapps/ROOT/
$ echo "Tomcat Test" > index.jsp
~~~

<!--ads-->

# Nginx

Nginx는 WEB서버입니다.  
정적파일 출력성능이 뛰어납니다.  
Load-balancing과 health-check 기능들을 가지고 WAS서버의 HA확보 기능도 제공합니다.  
HA 관련한 설정은 추후에 다루도록 하겠습니다.  

```yum```으로 설치를 진행했습니다.  
```yum```과 같은 패키지 자동 업데이트 툴을 사용할 수 없는 상황의 설치방법은 [Installing Nginx Open Source](https://www.nginx.com/resources/admin-guide/installing-nginx-open-source/)에서 확인해보시기 바랍니다.  

  * License : [FreeBSD License](http://nginx.org/LICENSE) - [Wikipedia BSD](https://en.wikipedia.org/wiki/BSD_licenses)

## Install Nginx Dependencies

Nginx를 사용하기 위해서는 pcre, zlib, OpenSSL 이 필요하고 사전에 설치되어 있어야합니다.  
그리고 이들을 설치하기 위해서는 gcc, gcc-c++이 사전에 설치되어있어야 합니다.  

### Install gcc packages

~~~terminal
$ yum install gcc gcc-c++
~~~

### Install pcre library

~~~terminal
$ yum install pcre
~~~

### Install zlib library

~~~terminal
$ yum install zlib
~~~

### Install OpenSSL library

~~~terminal
$ yum instsall openssl
~~~

## Install Nginx

### Check OS Information

OS마다 Nginx repository 정보가 다르기 때문에 OS 정보를 확인해야합니다.  

~~~terminal
$ cat /etc/issue
~~~

### Register Nginx Repository 

브라우저로 [http://nginx.org/packages/mainline/](http://nginx.org/packages/mainline/)에 접속해서 OS와 버전에 맞는 url를 확인합니다.  
그 url을 아래 baseurl로 입력하시면 됩니다.  

~~~bash
# vi /etc/yum.repos.d/nginx.repo

[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/mainline/rhel/6/$basearch/
gpgcheck=0
enabled=1
~~~

### Install Nginx By yum 

```yum```을 이용해서 Nginx를 설치합니다.  

~~~terminal
$ yum install nginx
~~~

만약에 실패했다면 repository의 base url이 현재 OS에 맞지 않게 입력되었을 가능성이 큽니다.  
base url을 수정 후에 yum을 clean, update 하고 재설치합니다.  

~~~terminal
$ yum clean
$ yum update
$ yum install nginx
~~~

## Start Nginx

Nginx를 실행합니다.  

~~~terminal
$ nginx
~~~

```curl``` 혹은 브라우저로 ```localhost```에 접속해서 Nginx가 실행 된 것을 확인합니다.  

~~~terminal
$ curl -I localhost
~~~

## Stop Nginx

Nginx를 중지합니다.  

~~~terminal
$ nginx -s stop
~~~

## Reload Configurre

Nginx의 변경된 configure를 reload합니다.  

~~~terminal
$ nginx -s reload
~~~

## Automatically Start Nginx 

OS 부팅시 자동으로 실행되도록 등록합니다.  

~~~terminal
$ chkconfig nginx on
~~~

<!--ads-->

# Integrate Nginx & Tomcat

Nginx와 Tomcat을 연동합니다.  

css, js, jpg, jpeg, gif, htm, html, swf 과 같은 정적인 파일에 대한 호출은 Nginx에서 처리하고  
그 외의 동적인 파일 형식은 tomcat에서 처리합니다.  

## Backup default.conf

Nginx를 설치하면 기본적으로 깔려있는 `default.conf`를 백업합니다.  

~~~terminal
$ cd /etc/nginx/conf.d
$ mv default.conf default.conf_bak
~~~

## Make tomcat.conf

`/etc/nginx/conf.d/` 디렉토리에 `tomcat.conf` 설정파일을 생성합니다.  
Nginx가 기동시에 `tomcat.conf` 설정파일을 활용합니다.  

~~~terminal
$ vi /etc/nginx/conf.d/tomcat.conf
~~~

`tomcat.conf` 설정파일을 아래와 같이 작성합니다.  

~~~nginx
upstream tomcat {
    ip_hash;
    server localhost:8080;
}

server {
    listen 80;
    server_name domain80.com www.domain80.com;

    access_log /var/log/nginx/test1.log;

    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
    }

    location ~ \.(css|js|jpg|jpeg|gif|htm|html|swf)$ {
        root /usr/share/nginx/html;
        index index.html index.html;
    }

    location ~ \.jsp$ {
        proxy_pass http://tomcat;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
}

server {
    listen 9090;
    server_name domain9090.com www.domain9090.com;

    access_log /var/log/nginx/test2.log;

    location / {
        proxy_pass http://tomcat;
    }

    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }

}
~~~

```localhost:8080``` 은 Tomcat서버의 IP, port입니다.  
Nginx와 Tomcat을 서로 다른 서버에 설치했을 때는 Tomcat서버의 내부 IP로 입력해주시면 됩니다.  

현재는 테스트 용도이기 때문에 하나의 서버에다가 Nginx, Tomcat을 같이 띄워지만  
보통 WAS서버는 보안상의 이유로 WEB서버와 물리적으로 분리시킵니다.  

현재 Nginx의 80포트와 9090포트를 열어놨습니다.  
9090포트는 50x 에러페이지 외에는 모두 Tomcat을 향하도록 세팅했습니다.  
80포트는 정적인 파일은 Nginx로 동적인 파일은 Tomcat을 향하도록 세팅했습니다.  
필요에 따라 하나만 사용하시면 됩니다.  

### Change nginx.conf

`/etc/nginx/nginx.conf` 파일을 수정합니다.  

~~~nginx

...

user nginx nginx;
worker_processes 4;

...

~~~

  * user : Nginx는 root로 실행되더라도 실제 worker는 nginx계정으로 실행되도록 설정합니다.  
    worker를 root계정으로 실행하게 되면 보안상의 문제가 있습니다.  
    OS에다가 nginx라는 계정을 만들어두진 않았지만 상관없이 처리 됩니다.  
  * worker_process : worker의 프로세스 개수를 설정합니다.  
    CPU가 1 core라면 프로세스 개수를 2개 이상으로해봤자 효과가 없습니다.  
    CPU가 N core라면 프로세스 개수를 N개로 해주는 것이 좋습니다.  

### Reload Nginx Configure

설정파일이 변경되었으니 reload 시킵니다.  

~~~terminal
$ nginx -s reload
~~~

### Check

8080포트로 열려있는 Tomcat의 index.jsp파일을 Nginx의 80포트로 접속합니다.  

~~~terminal
$ curl localhost/index.jsp
~~~

브라우저로 `localhost/index.html`에도 접속해봅니다. Nginx 페이지가 출력될 것입니다.  

html과 같은 정적인 파일 호출은 Nginx에 설정된 `/usr/share/nginx/html/` 경로의 파일들을 호출합니다.  
jsp같은 동적인 파일 호출은 80포트로 접속함에 불구하고 Tomcat의 `/usr/local/server/tomcat/webapps/ROOT/` 경로의 파일을 호출하는 것을 확인했습니다.  

<br/>

그리고 jsp파일을 호출하더라도 접속정보는 Nginx로 출력됩니다.  
사용자는 Nginx 뒤에 어떤 WAS가 구동중인지 확인할 수 없습니다.  

~~~terminal
$ curl -I localhost/index.jsp
~~~

