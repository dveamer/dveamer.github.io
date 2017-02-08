---
layout: post
title:  "Nginx SSL 적용방법"
date:   2017-01-25 12:00:00
categories: BackEnd 
tags: Setting Nginx SSL 
---

인증기관(CA)으로부터 인증서를 발급 받은 이후  
Nginx에 SSL을 적용하는 방법을 설명합니다.  

<!--more-->

# Install OpenSSL

일단 기존에 OpenSSL이 설치 되어있는지 확인합니다.   

~~~terminal
$ openssl version
~~~

설치되어있지 않다면 설치하시면 됩니다.  

~~~terminal
$ yum install openssl
~~~

설치가 되어있었다면 가장 최신 버전으로 업데이트를 추천합니다.  
좀 과거의 일이긴하지만 openssl로 인해 ssl에 Heartbleed라는 취약점이 생겼었습니다.  
최신버전으로 업데이트만하면 취약점을 잡을 수 있지만 아직까지도 업데이트를 하지않고  
취약점에 노출되어있는 사이트들이 굉장히 많다고 합니다.  

~~~terminal
$ yum update openssl
~~~

# Setting Nginx

## Upload Certificates & Private key

만드신 private-key파일과 제공받은 인증서 파일을 서버로 업로드해야합니다.  

sftp, ftp 등과 같은 방식으로 업로드 해주시면 됩니다.  
업로드하실 때는 root가 아닌 일반 계정으로 올리셔도 되며 임의의 디렉토리에 올려두시면 됩니다.  

저는 nginx라는 일반 계정으로 올리고 홈디렉토리 아래에 ssl 이라는 디렉토리를 만들어서 올리도록하겠습니다.  

~~~terminal
nginx$ mkdir ~/ssl
nginx$ cd ~/ssl
nginx$ pwd
~~~

만약 인증서 생성은 다른 사람(혹은 다른 팀)에서 진행했고  
본인은 모든 파일을 전달만 받으시는 경우가 있어서 파일에 대한 간략한 설명을 적겠습니다.  

 - private.key : 서버쪽 비공개키로 절대 외부에 노출되면 안됩니다.  
 - example.com.crt : 해당 도메인에 대해서 발급받으신 인증서 입니다.  
 - ca.crt : ROOT CA 인증서입니다.  
 - sub.ca.crt : 중계 CA 인증서입니다.  

## Setting Certificates & Private Key 

업로드한 파일들을 nginx에 적합하게 변경하고 세팅해야합니다.  
이번 작업은 일반 계정이 아닌 root 계정으로 진행합니다.  

### Make a Directory for SSL

일단 ssl 관련 파일을 보관할 디렉토리를 만들고 
root 계정만 read, write, delete가 가능하도록 접근권한을 설정합니다.  

~~~terminal
$ mkdir /etc/nginx/ssl
$ chmod 700 /etc/nginx/ssl
~~~

### Move the Private.Key file 

아까 업로드한 파일 중 private.key파일은 그냥 ```/etc/nginx/ssl```로 복사하고 권한설정을 합니다.  

~~~terminal
$ cd /home/nginx/ssl
$ cp example.com.key /etc/nginx/ssl
$ chmod 600 /etc/nginx/ssl/example.com.key
~~~

### Combine the Certificate files  

업로드한 파일 중 crt 파일들을 하나로 합쳐서 ```/etc/nginx/ssl```로 위치시키고 권한설정을 합니다.  
만약에 제공받은 crt 파일이 총 2개라면 그 중 하나는 CA 관련 인증서가 이미 합쳐져있는 상태일 겁니다.  
최종적으로 도메인 인증서 다음에 CA 인증서 순서로 합치기면 하면 문제 없을 것으로 예상됩니다.  

~~~terminal
$ cd /home/nginx/ssl
$ cat example.com.crt > /etc/nginx/ssl/unified-example.com.crt 
$ cat sub.ca.crt >> /etc/nginx/ssl/unified-example.com.crt 
$ cat ca.crt >> /etc/nginx/ssl/unified-example.com.crt 
$ chmod 600 /etc/nginx/ssl/unified-example.com.crt
~~~

### Remove the files which you uploaded

보안상 문제가 될 수 있으니 nginx 계정으로 다시 접속해서 업로드한 파일들을 지워줍니다.  

~~~terminal
nginx$ rm -rf ~/ssl/*
~~~

### Configure Nginx XML

~~~terminal
$ vi /etc/nginx/conf.d/tomcat.conf
~~~

ssl_certificatte 와 ssl_certificatte_key 를 절대주소로 설정해줍니다.  
443 port의 ssl 기능을 on 시켜주시고 80 port는 443 port로 rewrite 시키도록 세팅합니다.    

~~~vim

...

ssl_certificatte /etc/nginx/ssl/unified-example.com.crt;
ssl_certificate_key /etc/nginx/ssl/private.key;

server {
    listen 80;
    server_name www.example.com example.com;
    
    rewrite ^ https://$server_name:443?request_uri? permanent;
}

server {
    listen 443;
    server_name www.example.com example.com;
    
    ....
    
    ssl on;
    
    ....
    
}
~~~

### Restart nginx

설정을 변경했으니 재기동시켜줍니다.  

~~~terminal
$ nginx -s stop
$ nginx 
~~~

## Configure server.xml of Tomcat

Tomcat 쪽도 설정을 수정해줘야합니다.  
SSL을 적용하는 service의 server.xml 파일을 수정해야합니다.  

~~~terminal
$ $CATALINA_HOME/conf/server.xml
~~~

Connector 관련 설정에 ```scheme="https"```와 ```SSLEngine="on"``` 설정을 추가 합니다. 

~~~vim
...

<Server>
  ...
  
  <Service .... >
    <Connector protocol="HTTP/1.1"
      ....
      scheme="https"
      ....
    />
  
    <Connector protocol="AJP/1.3"
      ....
      SSLEngine="on"
      ....
    />
  
  ...
</Server>

...
~~~

