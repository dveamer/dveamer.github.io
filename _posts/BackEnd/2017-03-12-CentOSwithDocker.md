---
layout: post
title:  "Install CentOS with Docker"
date:   2017-03-11 12:00:00
categories: BackEnd
tags: Setting Docker CentOS
---

![Docker](https://www.docker.com/sites/default/files/social/docker-facebook-share.png){:class="imgTitle"}
![CentOS](https://raw.githubusercontent.com/docker-library/docs/c4df0024e2cad985326dc38f6b6ce39abeab59c5/centos/logo.png){:class="imgTitle"}  
(이미지 출처 : [https://www.docker.com](https://www.docker.com), [https://hub.docker.com](https://hub.docker.com/_/centos/) )  

이 문서는 Docker를 이용해서 CentOS를 구성하는 내용을 다룹니다.  

다만 주 목적이 Nginx, Tomcat을 실습하기 위함이라  
Docker에 대해서는 자세히 다루지 않으며 가장 간단한 방법으로 사용합니다.  

<!--more-->

<br/>

이 문서를 작성하게 된 계기는 이렇습니다.  

Nginx, Tomcat 에 대해서 실습하기 위해서 환경을 구성하고자합니다.  

근데 시작하자마자 부딪힌 문제가 있는데..  
모두 Windows PC를 가지고 있지만  
실제로 Nginx, Tomcat을 사용할 환경은 RHEL(RedHat Enterprise Linux) 환경이라는 점입니다.  

실제와 비슷한 환경에서 실습을 하기 위해  
Docker를 이용해서 CentOS를 구성하고 그 위에서 Nginx, Tomcat을 실습하고자 합니다.  

# Docker 설치

[Docker Windows에 설치하기](http://pseg.or.kr/pseg/infoinstall/6076) 링크를 참고해서 Docker를 설치해주세요.  

안타깝게도 저는 집에서 Windows가 아닌 Ubuntu 를 사용하기 떄문에 위 링크자료를 직접 수행해보진 못 했습니다.  

Ubuntu 에서는 이 한줄이면 끝납니다.  
~~~terminal 
$ sudo apt-get install docker.io
~~~

# 이미지 다운로드

Windows에서는 ```Docker Quickstart Terminal``` 를 실행 후 아래 명령어를 입력하세요.

명령어에서 sudo는 Ubuntu에서 root권한으로 실행하기 위한 명령어 입니다.  
Windows에서 진행하시는 분들은 sudo를 빼고 진행하시기 바랍니다.  

## CentOS 이미지 검색

~~~terminal
$ sudo docker search centos
NAME                                   DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                                 The official build of CentOS.                   3169      [OK]       
jdeathe/centos-ssh                     CentOS-6 6.8 x86_64 / CentOS-7 7.3.1611 x8...   62                   [OK]
jdeathe/centos-ssh-apache-php          CentOS-6 6.8 x86_64 - Apache / PHP-FPM / P...   25                   [OK]
nimmis/java-centos                     This is docker images of CentOS 7 with dif...   23                   [OK]
consol/centos-xfce-vnc                 Centos container with "headless" VNC sessi...   22                   [OK]
gluster/gluster-centos                 Official GlusterFS Image [ CentOS-7 +  Glu...   18                   [OK]
million12/centos-supervisor            Base CentOS-7 with supervisord launcher, h...   13                   [OK]
torusware/speedus-centos               Always updated official CentOS docker imag...   8                    [OK]
egyptianbman/docker-centos-nginx-php   A simple and highly configurable docker co...   6                    [OK]
nathonfowlie/centos-jre                Latest CentOS image with the JRE pre-insta...   5                    [OK]
centos/mariadb55-centos7                                                               4                    [OK]
consol/sakuli-centos-xfce              Sakuli JavaScript based end-2-end testing ...   3                    [OK]
harisekhon/centos-scala                Scala + CentOS (OpenJDK tags 2.10-jre7 - 2...   2                    [OK]
centos/redis                           Redis built for CentOS                          2                    [OK]
harisekhon/centos-java                 Java on CentOS (OpenJDK, tags jre/jdk7-8)       2                    [OK]
blacklabelops/centos                   CentOS Base Image! Built and Updates Daily!     1                    [OK]
freenas/centos                         Simple CentOS Linux interactive container       1                    [OK]
darksheer/centos                       Base Centos Image -- Updated hourly             1                    [OK]
timhughes/centos                       Centos with systemd installed and running       1                    [OK]
januswel/centos                        yum update-ed CentOS image                      0                    [OK]
smartentry/centos                      centos with smartentry                          0                    [OK]
vcatechnology/centos                   A CentOS Image which is updated daily           0                    [OK]
otagoweb/centos                        Apache (with PHP7), built on CentOS 7           0                    [OK]
termbox/centos                         CentOS                                          0                    [OK]
repositoryjp/centos                    Docker Image for CentOS.                        0                    [OK]
~~~

대략 20개가 넘는 이미지가 검색되었습니다.  
설명을 읽어보면 가장 맨 위에 있는 것이 CentOs의 공식 이미지입니다.  
  
그 외의 이미지들은 CentOS 위에 Java 설치한 것도 있고 Redis를 설치한 것들도 있고.. 그렇습니다.  

잘 찾아보면 Nginx, Tomcat이 설치 된 이미지도 이미 있을 수 있지만  
우리는 맨땅에서 시작해보기 위해 CentOS에서 제공한 공식버전을 다운받습니다.  

## 이미지 다운로드

### 이미지 조회 (다운로드 전)

~~~terminal
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
~~~ 

가지고 있는 docker 이미지를 조회해봤습니다. 아무것도 없죠?  


### CentOS 이미지 다운로드

~~~terminal
$ sudo docker pull centos:6.6
6.6: Pulling from centos
3690474eb5b4: Pull complete 
d0d663863c34: Pull complete 
Digest: sha256:0b0088e773be0160392be352fd3b07f4fec1f4c1548f98f7d0c2ab0c01bad6ec
Status: Downloaded newer image for centos:6.6
~~~

가장 최신버전을 받으시기 위해서는 ```centos:lastest```로 입력하시면 되고  
만약 ```centos:7```로 입력하시면 7버전 중 가장 최신버전이 다운로드 될 것으로 예상됩니다.  

저는 6.6 버전의 CentOS를 다운받았습니다. 별다른 이유는 없습니다. 원하시는 버전을 받으시면 됩니다.  


### 이미지 조회 (다운로드 후)

~~~terminal
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              6.6                 d0d663863c34        6 months ago        202.6 MB
~~~ 

centdos 이미지가 조회되는 것을 확인할 수 있습니다.  

# Container 실행

뭐든지 그렇듯이..  
Docker를 그냥 사용하는 것은 단순하지만 잘 사용하려면 복잡합니다.  
  
"CentOS를 이용할 것이고  
yum을 이용해서 os 업데이트를 하고  
yum을 이용해서 Nignx를 설치하고  
설치하는 중에 다운 받았지만 이제는 불필요한 내용물들을 지운다."  
라는 내용의 스크립트를 Dockerfile 라는 파일명으로 만들어서 사용해야하니다.  

게다가 root 권한과 관련해서 보안문제도 있고 logging은 어떻게 할거며.. 그 외 신경써야하는 다양한 문제점들이 많습니다.  

하지만 지금은 Docker 학습시간이 아니고  
우리는 그냥 간단한 실습만을 목적으로 하기 때문에 다양한 문제점을 너그럽게 수용하며 가장 간단한 방법으로 진행합시다.  

## CentOS Container 최초실행

~~~terminal
$ sudo docker run -i -t -p 8081:80 --name NginxServer centos:6.6 /bin/bash
[root@a9003c86085a /]# 
~~~

centos:6.6 이미지의 ```/bin/bash```를 container로 실행시키며 NginxServer 라는 이름을 줬습니다.  

그리고 8081 port를 container의 80 port로 연결되도록 설정했습니다.  

### Container 빠져나오기

~~~terminal
[root@a9003c86085a /]# exit
exit
$ 
~~~

## CentOS Container 시작/종료

### Container 종료

~~~terminal 
$ sudo docker stop NginxServer
NginxServer
~~~

### Container 조회

~~~terminal
$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                        PORTS               NAMES
a9003c86085a        centos:6.6          "/bin/bash"         About a minute ago   Exited (127) 32 seconds ago                       NginxServer        
~~~

-a 옵션을 빼면 실행 중인 container만 조회 됩니다.  

### Container 시작

~~~terminal 
$ sudo docker start NginxServer
NginxServer
~~~

## Container 재실행

~~~terminal
$ sudo docker exec -i -t NginxServer /bin/bash
[root@a9003c86085a /]# 
~~~

최초실행시와 동일한 container id(a9003c86085a)로 접속한 것을 확인할 수 있습니다.  

# 확인작업 

## OS 확인 

### Container 실행 전

~~~terminal 
$ grep . /etc/*-release
/etc/lsb-release:DISTRIB_ID=Ubuntu
/etc/lsb-release:DISTRIB_RELEASE=14.04
/etc/lsb-release:DISTRIB_CODENAME=trusty
/etc/lsb-release:DISTRIB_DESCRIPTION="Ubuntu 14.04.5 LTS"
/etc/os-release:NAME="Ubuntu"
/etc/os-release:VERSION="14.04.5 LTS, Trusty Tahr"
/etc/os-release:ID=ubuntu
/etc/os-release:ID_LIKE=debian
/etc/os-release:PRETTY_NAME="Ubuntu 14.04.5 LTS"
/etc/os-release:VERSION_ID="14.04"
/etc/os-release:HOME_URL="http://www.ubuntu.com/"
/etc/os-release:SUPPORT_URL="http://help.ubuntu.com/"
/etc/os-release:BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
~~~

저의 경우는 OS가 ubuntu라고 출력됩니다.  
Windows의 경우에는 어떻게 뜰지 모르겠네요.   

### Container 실행 후 

~~~terminal 
[root@a9003c86085a /]# grep . /etc/*-release
/etc/centos-release:CentOS release 6.6 (Final)
/etc/redhat-release:CentOS release 6.6 (Final)
/etc/system-release:CentOS release 6.6 (Final)
~~~

이미지 실행 전에는 OS가 Ubuntu 였지만 실행 후에는 CentOS인 것을 확인합니다.  

## Container 종료시 작업 유지여부 확인

### Container 종료전 

Container에 접속 후 디렉토리를 하나 생성해봅니다.  

~~~terminal
[root@a9003c86085a /]# mkdir /home/test1
[root@a9003c86085a /]# ll /home
total 4
drwxr-xr-x 2 root root 4096 Mar 12 10:30 test1
~~~

### Container 종료 후

Container를 완전히 종료 후에 다시 접속해서 위 디렉토리가 그대로 남아있는지 확인합니다.  

~~~termincal
[root@a9003c86085a /]# exit
exit
$ sudo docker stop NginxServer
NginxServer
$ sudo docker start NginxServer
NginxServer
$ sudo docker exec -i -t NginxServer /bin/bash
[root@a9003c86085a /]# ll /home
total 4
drwxr-xr-x 2 root root 4096 Mar 12 10:30 test1
~~~

## Port 오픈 확인

Container에서 아래와 같이 python에서 제공하는 SimpleHTTPServer를 실행합니다.  
80 port를 오픈하는 명령어입니다.  

~~~termincal
[root@a9003c86085a /]# python -m SimpleHTTPServer 80
~~~

PC의 브라우저를 열고 ```http://localhost:8081```로 접속합니다.  
브라우저에서 container의 root 디렉토리가 조회되는 것을 확인할 수 있습니다.  
그리고 SimpleHTTPServer를 실행한 terminal에서 브라우저의 접속로그를 확인합니다.  

외부에서 8081로 접근시 Docker container의 80 Port로 연결되는 것을 확인했습니다.  

# 마무리

Docker를 이용해서 Nginx를 세팅할 수 있는 CentOS Container 구성했습니다.  
이제 Tomcat을 위한 Container를 하나 더 구성해야겠지요?  
그 과정은 직접 해보시기 바랍니다.  

동일한 스팩으로 CentOS Container를 구성한다면 위의 내용 중 container 이름과 port만 변경해서 진행하시면 됩니다.  

이제 Nginx, Tomcat을 공부하고 실습하는 것만 남았네요!  

