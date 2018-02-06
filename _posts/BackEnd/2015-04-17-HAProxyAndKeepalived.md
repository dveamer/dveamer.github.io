---
layout: post
title:  "HAProxy & Keepalived"
date:   2015-04-17 08:00:00
lastmod: 2018-02-06 00:00:00 
categories: Architecture
tags: HAProxy Keepalived L4 VRRP RedHat RHEL
---

![Haproxy](http://www.haproxy.org/img/logo-med.png) ![Keepalived](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRr9Rnjps3xzfDUWDJRvgCfja9HZwVDQ7B22H5fDspl0SdJvDNMZA)  
( 이미지 출처 : [http://www.haproxy.org](http://www.haproxy.org), [https://encrypted-tbn0.gstatic.com](https://encrypted-tbn0.gstatic.com) )

요즘 일반적인 WEB-WAS 서비스의 경우 Apache, NginX 와 같은 WEB서버를 이용해서 WAS의 이중화 서비스를 구성 가능합니다.  
하지만 시스템 형태가 HTTP프로토콜을 사용하지 않고 자체 프로토콜로 소켓통신하는 서비스는 이중화를 위해서는 L4외에 마땅히 좋은 방법을 찾지 못했습니다.  

HAProxy를 L4로 이용해서 백엔드 서버를 Active-Active 형태로 이중화를 구성했습니다.  
HAProxy가 죽을 경우를 대비해 Keepalived 이용해서 HAProxy를 Active-Standby 형태로 이중화 했습니다.  

<!--more-->

# Concept

## HAProxy

  * 고가용성(High-Availability) solution
  * 사용하는 기능
    - 네트워크 스위치의 L4, L7 기능 -> High-Availiablility (고가용성)
    - Load-balancing (부하분산)
    - Proxy for TCP, HTTP
  ![HAProxy](/images/post_img/HaproxyKeepalived/HAProxy.png "HAProxy")

  * 장점
    - 비싼 하드웨어 구매 없이 L4 를 구성 가능합니다.
    - HTTP 같은 표준 프로토콜이 아닌 TCP socket 통신에 대해서도 이중화 처리 가능합니다.
    - HTTP 통신의 경우 WEB-Server(Nginx, Apache 등)를 이중화 시켜줄 수 있습니다. WEB서버 설정으로만으로도 WAS는 이중화 가능합니다.
    - HAProxy 설정추가를 통해 scale-out 도 쉽게 가능합니다.

  * References
    - GitHub, Bitbucket, Stack Overflow, Reddit, Tumblr, Twitter and ETC

## Keepalived

  * Routing software
  * VRRP 프로토콜을 이용해서 Active-Standby 가능하게 합니다.

  ![VRRP_Keepalived](/images/post_img/HaproxyKeepalived/lvs-two-tier-1.png "VRRP_Keepalived")

  ( 이미지 출처 : [access.redhat.com](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html-single/Load_Balancer_Administration/index.html))

  * 사용하는 기능
    - High-Availiablility (고가용성) ; VRRP 프로토콜 이용

    ~~~
      < VRRP 란? >
      - Virtual Router Redundancy Protocol
      - 하나 이상의 Standby 라우터를 가질 수 있는 방법을 제공하는 인터넷 프로토콜입니다.
      - VIP(Virtual IP)가 필요합니다.
      - 각 서버들은 Master, Standby 으로 역할이 나뉘며 Master는 VIP 를 사용합니다.
      - Health-ckeck를 통해 Master 에게 문제가 발생이 인지되는 경우,
        Master 서버의 역할이 Standby 서버로 전환됩니다.
        Standby 서버중 하나의 역할이 Master 로 전환 그리고 VIP를 넘겨받습니다.
    ~~~

  * 주의사항
    - Active와 stnadby 장비는 인증절차 없이 양방향 ssh접속필요 합니다. (ssh-keygen -t rsa로 구글링)

## HAProxy with Keepalived

  * 다양한 방법으로 구성할 수 있지만 아래와 같이 구성 했습니다.
  * L4 용 Routing 서버 두대(Active, Standby) 에 각각 Keepalvied, HAProxy 세팅합니다.
  * Keepalived 로 L4용 Routing 서버의 고가용성(HA) 보장합니다.
  ![HAProxy_Keepalived_01](/images/post_img/HaproxyKeepalived/HAProxy_Keepalived_01.png "HAProxy_Keepalived_01")

  * Active Routing 서버의 문제 발생시
  ![HAProxy_Keepalived](/images/post_img/HaproxyKeepalived/HAProxy_Keepalived.png "HAProxy_Keepalived")
  (출처 : [helloworld.naver.com](http://helloworld.naver.com/helloworld/textyle/284659))


# Google Trend

상대적인 관심도 그래프로써 수치는 상대적 %값

![HAProxy & Keepalived](/images/post_img/HaproxyKeepalived/googletrend_01.png)

![HAProxy & L4](/images/post_img/HaproxyKeepalived/googletrend_02.png "HAProxy & L4")


## References

  * Keepalived/ipvs을 이용한 리눅스 L4 - DR(Direct Routing) 구축
    - http://netggio.pe.kr/wiki/index.php/Keepalived/ipvs을_이용한_리눅스_L4_-_DR(Direct_Routing)_구축
  * [L4/L7 스위치의 대안, 오픈 소스 로드 밸런서 HAProxy](http://helloworld.naver.com/helloworld/textyle/284659)
  * [HAProxy Wikipedia](http://en.wikipedia.org/wiki/HAProxy)
  * [Keppalived](http://www.keepalived.org/)



# Environment

  * RHEL 5.5
  * 1 core 2 GB / 2 core 4 GB

마스터 서버를 1core 로 설정했습니다.  

# HAProxy 설치 & 설정

Active, Standby 서버 모두 동일하게 진행하고 설정파일 수정만 다르게 진행됩니다.  

## 계정생성 

~~~terminal
$ groupadd hapgrp
$ useradd -g hapgrp hapusr
$ passwd hapusr
~~~

## HAProxy 설치

Ubuntu에서는 apt-get을 이용해서 쉽게 설치했는데  
Redhat에서 yum을 이용해 설치를 하다가 실패했습니다.  
[Linux에서 컴파일 설치법을 알아야하는 이유](/BackEnd/CompilinginstallationOnLinux.html)글에서처럼 환경문제로 yum을 사용 못한것이 아니라  
yum이 정상적으로 사용되는 상황임에도 불구하고 설치에 실패했습니다.  

  * haproxy package 를 검색실패했습니다.
  * fedora 의 EPEL repository를 이용하라는 가이드가 많지만 EPEL 에서도 검색실패 했습니다.
  * RHEL 6.4 부터 EPEL 과 policy 문제로 인해 EPEL repository 에서 HAProxy 제거된 것으로 파악됩니다.
    http://serverfault.com/questions/482017/installing-haproxy-on-centos-6-3  
    http://serverfault.com/questions/645343/install-haproxy-on-redhat-6-6  
  * RHEL 7 에서는 HAProxy 를 내장한 것으로 예상됩니다. ( 7 document에는 HAProxy 내용이 포함되어있음 )
  * HAProxy를 보유한 repository를 찾아보려고했으나 잘 모르겠어서 진행 안했습니다.

그래서 컴파일을 통한 설치를 진행했습니다.  

~~~terminal
root@localhost:~$ yum install openssl-devel
root@localhost:~$ cd /usr/local/src
root@localhost:src$ wget http://www.haproxy.org/download/1.5/src/haproxy-1.5.12.tar.gz
root@localhost:src$ tar xvfz haproxy-1.5.12.tar.gz
root@localhost:src$ cd haproxy-1.5.12
root@localhost:haproxy-1.5.12$ make TARGET=linux26 USE_OPENSSL=1 ADDLIB=-lz
root@localhost:haproxy-1.5.12$ make install
root@localhost:haproxy-1.5.12$ cp ./examples/haproxy.init /etc/rc.d/init.d/haproxy
root@localhost:haproxy-1.5.12$ chmod 755 /etc/rc.d/init.d/haproxy
root@localhost:haproxy-1.5.12$ mkdir -p /etc/haproxy
root@localhost:haproxy-1.5.12$ cp ./examples/haproxy.cfg /etc/haproxy/
root@localhost:haproxy-1.5.12$ mkdir -p /etc/haproxy/errors
root@localhost:haproxy-1.5.12$ cp ./examples/errorfiles/* /etc/haproxy/errors/
~~~

  * Required Softlinks 설정

~~~terminal
root@localhost:~$ ln -s /usr/local/sbin/haproxy /usr/sbin/haproxy
~~~

## haproxy.cfg 설정


~~~bash
 # vim /etc/haproxy/haproxy.cfg

global
  log 127.0.0.1 local0
  maxconn 4096
  daemon
  uid 99
  gid 99
defaults
  log     global
  mode    http
  option  httplog
  option  dontlognull
  timeout server 5s
  timeout connect 5s
  timeout client 5s
listen tcp_service_a :14900
  mode tcp
  option tcplog
  option tcp-check
  # tcp-check send PING
  # tcp-check expect string PONG
  balance roundrobin
  server tcp_service_a_01 xxx.xxx.xxx.001:4900 check
  server tcp_service_a_02 xxx.xxx.xxx.002:4900 check
listen stats :80
  mode http
  log global
  maxconn 10
  stats enable
  stats refresh 30s
  stats uri /haproxy.stats
~~~

  * HAProxy의 listen port 와 application의 port는 같아도 됩니다.  
  * 만약 HAProxy와 application을 같은 서버내에 구성하게되면 포트 충돌이 일어나므로 port를 다르게 구성해야하며 client는 HAProxy listen port 로 요청해야합니다.  

  * default health-check 는 해당 port 에 connection 여부로 체크를 합니다.  
  * tcp-check send/expect 를 이용해서 application 레벨의 체크를 진행 가능합니다.  

  * root로 실행하더라도 haproxy 프로세스의 실소유자는 uid에 해당되는 계정입니다.  
    숫자가 아닌 계정명을 입력하면 실행시 에러가 납니다.  
    앞서 생성했던 계정 hapusr, hapgrp 의 uid, gid를 입력하시기 바랍니다.   

    ~~~terminal
    // uid, gid 확인방법
    $ cat /etc/passwd | grep hapusr
    $ cat /etc/group | grep hapgrp
    ~~~

## Log 설정

HAProxy log를 남길 폴더를 생성하고 권한을 제한합니다.  

~~~terminal
$ mkdir -p /var/log/haproxy
$ chown hapusr:hapgrp /var/log/haproxy
$ chmod 700 /var/log/haproxy
~~~

/etc/rsyslog.d/haproxy.conf 파일을 만들고 설정합니다.  

~~~bash
 # vim /etc/rsyslog.d/haproxy.conf

$ModLoad imudp
$UDPServerRun 514
$template Haproxy, "%msg%\n"
local0.=info -/var/log/haproxy/haproxy.log;Haproxy
local0.notice -/var/log/haproxy/haproxy-status.log;Haproxy
local0.* ~
~~~

rsyslog 프로그램을 restart 시킵니다.  

~~~terminal
$ service rsyslog restart
~~~

## 실행 및 종료

### 실행권한 설정

~~~terminal
$ echo '# User privilege specification' >> /etc/sudoers
$ echo 'hapusr ALL=NOPASSWD: /etc/init.d/haproxy * ' >> /etc/sudoers
$ echo 'hapusr ALL=NOPASSWD: service haproxy * ' >> /etc/sudoers
~~~ 

hapusr 계정에게 haproxy에 대해서 sudo 권한을 제공했습니다.  

### 실행

~~~terminal
$ /etc/init.d/haproxy start
OR
$ service haproxy start
~~~

hapusr 계정으로도 실행가능합니다.  

~~~terminal
hapusr$ sudo /etc/init.d/haproxy start
OR
hapusr$ sudo service haproxy start
~~~

실행된 프로세스의 소유자가 hapusr인 것을 확인할 수 있습니다.  

~~~terminal
$ ps -ef | grep haproxy 
~~~

만약 다른 소유자가 hapusr가 아니라면 haproxy.cnf 파일 설정시 uid, gid 설정을 다시 해보시기 바랍니다.  


### 종료

~~~terminal
$ /etc/init.d/haproxy
OR
$ service haproxy stop
~~~

역시 hapusr 계정으로도 종료 가능합니다.  

~~~terminal
hapusr$ sudo /etc/init.d/haproxy stop
OR
hapusr$ sudo service haproxy stop
~~~

### 부팅시 자동실행 설정

~~~terminal
$ chkconfig haproxy on
~~~


### Status 확인

브라우저로 http://xxx.xxx.xxx.xxx/haproxy.stats 에 접속해보시면 HAProxy의 현재상황을 확인가능합니다.  


# Keepalived 설치 & 설정

Active, Standby 서버 모두 동일하게 설치하고 설정파일 수정만 다르게 진행합니다.  

## Kernel Headers 설치

~~~console
$ yum -y install kernel-headers kernel-devel
~~~

## Keepalived 설치 

컴파일을 통해서 설치했습니다.  
필요한 버전에 맞춰서 다운받아 사용하세요.  

~~~terminal
// download
root@localhost:~$ cd /usr/local/src
root@localhost:src$ wget http://www.keepalived.org/software/keepalived-1.2.16.tar.gz
root@localhost:src$ tar -zxvf keepalived-1.2.16.tar.gz
~~~

~~~terminal
// compile
root@localhost:~$ cd /usr/local/src/keepalived-1.2.16
root@localhost:keepalived-1.2.16$ ./configure --with-kernel-dir=/lib/modules/$(uname -r)/build
root@localhost:keepalived-1.2.16$ make && make install
~~~

~~~terminal
// Required Softlinks 설정
root@localhost:~$ cd /etc/sysconfig
root@localhost:sysconfig$ ln -s /usr/local/etc/sysconfig/keepalived .
root@localhost:sysconfig$ cd /etc/rc3.d/
root@localhost:rc3.d$ ln -s /usr/local/etc/rc.d/init.d/keepalived S100keepalived
root@localhost:rc3.d$ cd /etc/init.d/
root@localhost:init.d$ ln -s /usr/local/etc/rc.d/init.d/keepalived .
~~~

## Active(MASTER) 서버의 Keepalived 설정

Active(MASTER) 로 사용할 서버의 설정을 진행합니다.  


### /etc/sysctl.conf 설정 수정 

일단 외부 IP와 바인딩 가능하도록 OS설정 수정이 필요합니다.  

~~~terminal 
$ echo 'net.ipv4.ip_nonlocal_bind=1' >> /etc/sysctl.conf
$ sysctl -p
~~~

### /usr/local/etc/sysconfig/keepalived 파일을 수정

~~~bash
 # vim /usr/local/etc/sysconfig/keepalived

 # 변경 전
KEEPALIVED_OPTION="-D"

 # 변경 후
KEEPALIVED_OPTION="-D -P -f /usr/local/etc/keepalived/keepalived.conf"
~~~

  * keepalived 실행시 입력되는 파라미터 값  
  * P 옵션은 VRRP 만 이용하겠다는 의미  
  * f 옵션은 configuration 위치 지정  

HAProxy와 함께 사용하면서 keepalived는 VRRP 기능만 이용할 것이라 P옵션을 사용했습니다.  
수동으로 설치하면서 configuration 위치를 찾지 못하는 현상이 있어서 직접 위치를 입력합니다.  


### /usr/local/etc/keepalived/keepalived.conf 파일 수정

파일이 없다면 생성하시면 됩니다.  

~~~bash
 # Active(MASTER) L4
 # vim /usr/local/etc/keepalived/keepalived.conf

 # Settings for notifications
global_defs {
    notification_email {
        your@emailaddress.com     # Email address for notifications
    }
    notification_email_from loadb01@domain.ext  # The from address for the notifications
    smtp_server 127.0.0.1     # You can specifiy your own smtp server here
    smtp_connect_timeout 15
}

 # Define the script used to check if haproxy is still working
vrrp_script chk_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

 # Configuation for the virtual interface
vrrp_instance VI_1 {    # set this to diffrent name on the other machine. When notifying events, the e-mail show you this name.

    interface eth0
    state MASTER        # set this to BACKUP on the other machine
    priority 101        # set this to 100 on the other machine
    virtual_router_id 51

    smtp_alert          # Activate email notifications

    authentication {
        auth_type PASS
        auth_pass myPassw0rd      # Set this to some secret phrase
    }

    # The virtual ip address shared between the two loadbalancers
    virtual_ipaddress {
        xxx.xxx.xxx.xxx
    }

    # Use the script above to check if we should fail over
    track_script {
        chk_haproxy
    }
}
~~~

  * Event가 발생하게되면 e-mail로 알림을 받게 됩니다. master, backup의 vrrp_instance 명칭을 다르게 세팅하지 않으면 상황파악 어렵습니다.


## Standby(BACKUP) 서버의 Keepalived 설정

Standby(BACKUP)로 사용될 서버의 설정을 진행합니다.  
위의 Active(MASTER) 설정과 딱 1개만 다르고 동일하게 진행해주시면 됩니다.  

HAProxy 설정파일(/usr/local/etc/keepalived/keepalived.conf)을 수정하는 내용만 차이가 있습니다.  

### /usr/local/etc/keepalived/keepalived.conf 파일 수정

파일이 없다면 생성하시면 됩니다.  
Active(MASTER)와 동일한 내용을 입력하고 아래 내용만 다르게 수정합니다.  

~~~bash
 # vim /usr/local/etc/keepalived/keepalived.conf

vrrp_instance VI_2 {
  ...
  state BACKUP        
  priority 101        
  ...
}
~~~


## 실행 & 종료 

### 실행 

~~~terminal
$ /etc/init.d/keepalived start
OR
$ service keepalived start
~~~

아래와 같은 메시지로 실행에 실패할 수 있습니다.  

~~~terminal 
Starting keepalived: /bin/bash: keepalived: command not found
~~~

그럴 때는 /etc/init.d/keepalived 파일 수정필요합니다.  

~~~bash
 # vim /etc/init.d/keepalived

 # 변경 전
start() {
  echo -n $”Starting $prog: ”
  daemon keepalived ${KEEPALIVED_OPTIONS}

 # 변경 후
start() {
  echo -n $”Starting $prog: ”
  daemon /usr/local/sbin/keepalived ${KEEPALIVED_OPTIONS}
~~~

### 종료

~~~terminal
$ /etc/init.d/keepalived stop
OR
$ service keepalived stop
~~~

### 부팅시 자동실행 설정

~~~terminal
$ chkconfig keepalived on
~~~

# Reference

  * [Keepalived 수동설치](http://www.cyberciti.biz/faq/rhel-centos-fedora-keepalived-lvs-cluster-configuration/)
  * [Keepalived 설정 for HAProxy](http://www.leaseweblabs.com/2011/09/setting-up-keepalived-on-ubuntu-load-balancing-using-haproxy-on-ubuntu-part-2/)
  * [Proxy keywords matrix](http://cbonte.github.io/haproxy-dconv/configuration-1.5.html#4.1)
  * [HAProxy 설치 및 운영](http://itscom.org/archives/4601)
  * [HAProxy tcp-check](http://cbonte.github.io/haproxy-dconv/configuration-1.5.html#4.2-option%20tcp-check)
  * [yum repo 삭제](https://www.howtoforge.com/community/threads/how-do-i-uninstall-rpm-packages.8/)
  * [HAProxy Log Setting](http://xmodulo.com/haproxy-http-load-balancer-linux.html)
  
# Reforence for future
  * [HAProxy 로 MySQL 로드밸런싱 구축하기 - CentOS 5.x](http://saksin.tistory.com/1108)
