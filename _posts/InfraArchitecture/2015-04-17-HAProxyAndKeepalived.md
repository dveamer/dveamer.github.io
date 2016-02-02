---
layout: post
title:  "HAProxy & Keepalived"
date:   2015-04-17 08:00:00
categories: Architecture
tags: HAProxy Keepalived L4 VRRP RedHat RHEL
---

![Haproxy](http://www.haproxy.org/img/logo-med.png) ![Keepalived](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRr9Rnjps3xzfDUWDJRvgCfja9HZwVDQ7B22H5fDspl0SdJvDNMZA)

WEB-WAS 서비스의 경우 일반적으로 Apache, NginX 와 같은 WEB서버를 이용해서 WAS의 이중화 서비스를 구성 가능합니다.
하지만 시스템 형태가 WEB서버를 사용하지 않고 데몬을 띄운채 자체 프로토콜로 소켓통신하는 서비스는 이중화를 위해서는 L4외에 마땅히 좋은 방법을 찾지 못했습니다.

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

  (출처 : [access.redhat.com](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html-single/Load_Balancer_Administration/index.html))

  * 사용하는 기능
    - High-Availiablility (고가용성) ; VRRP 프로토콜 이용

    ```
      < VRRP 란? >
      - Virtual Router Redundancy Protocol
      - 하나 이상의 Standby 라우터를 가질 수 있는 방법을 제공하는 인터넷 프로토콜입니다.
      - VIP(Virtual IP)가 필요합니다.
      - 각 서버들은 Master, Standby 으로 역할이 나뉘며 Master는 VIP 를 사용합니다.
      - Health-ckeck를 통해 Master 에게 문제가 발생이 인지되는 경우,
        Master 서버의 역할이 Standby 서버로 전환됩니다.
        Standby 서버중 하나의 역할이 Master 로 전환 그리고 VIP를 넘겨받습니다.
    ```

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

# Installation

## Environment
  * RHEL 6.6
  * 2 core 4 GB / 2 core 4 GB

## HAProxy

  * Active, Standby L4 서버 모두 동일하게 진행합니다. 설정파일 수정만 다르게 진행됩니다.
  * HAProxy 설치 : 수동으로 설치
    - yum 을 이용한 설치 실패 : Ubuntu에서는 쉽게 깔았는데..
      * haproxy package 를 검색실패했습니다.
      * fedora 의 EPEL repository를 이용하라는 가이드가 많지만 EPEL 에서도 검색실패 했습니다.
      * RHEL 6.4 부터 EPEL 과 policy 문제로 인해 EPEL repository 에서 HAProxy 제거된 것으로 파악됩니다.
        http://serverfault.com/questions/482017/installing-haproxy-on-centos-6-3
        http://serverfault.com/questions/645343/install-haproxy-on-redhat-6-6
      * RHEL 7 에서는 HAProxy 를 내장한 것으로 예상됩니다. ( 7 document에는 HAProxy 내용이 포함되어있음 )
      * HAProxy를 보유한 repository를 찾아보려고했으나 잘 모르겠어서 진행 안했습니다.

    - 수동으로 설치

    ```
    [root@localhost ~] # yum install openssl-devel
    [root@localhost ~] # cd /usr/local/src
    [root@localhost src] # wget http://www.haproxy.org/download/1.5/src/haproxy-1.5.12.tar.gz
    [root@localhost src] # tar xvfz haproxy-1.5.12.tar.gz
    [root@localhost src] # cd haproxy-1.5.12
    [root@localhost haproxy-1.5.12] # make TARGET=linux26 USE_OPENSSL=1 ADDLIB=-lz
    [root@localhost haproxy-1.5.12] # make install
    [root@localhost haproxy-1.5.12] # cp ./examples/haproxy.init /etc/rc.d/init.d/haproxy
    [root@localhost haproxy-1.5.12] # chmod 755 /etc/rc.d/init.d/haproxy
    [root@localhost haproxy-1.5.12] # mkdir -p /etc/haproxy
    [root@localhost haproxy-1.5.12] # cp ./examples/haproxy.cfg /etc/haproxy/
    [root@localhost haproxy-1.5.12] # mkdir -p /etc/haproxy/errors
    [root@localhost haproxy-1.5.12] # cp ./examples/errorfiles/* /etc/haproxy/errors/
    ```

  * Required Softlinks 설정

    ```
    [root@localhost haproxy-1.5.3] # cd /usr/sbin
    [root@localhost sbin] # ln -s /usr/local/sbin/haproxy haproxy
    ```

  * Configuration
    - default health-check 는 해당 port 에 connection 여부로 체크를 합니다.
    - tcp-check send/expect 를 이용해서 application 레벨의 체크를 진행 가능합니다.
    - HAProxy의 listen port 와 application의 port는 같아도 된다. 만약 같은 서버내에 구성하게되면 포트 충돌이 일어나므로 다르게 구성해야하며 client는 HAProxy listen port 로 송신해야합니다.
    - /etc/haproxy/haproxy.cfg 변경합니다.
      vi /etc/haproxy/haproxy.cfg

      ```
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
      ```
    - Log 설정
      

      ```
        # HAProxy log 폴더 생성
        cd /var/log
        mkdir haproxy
      ```

      ```
        # /etc/rsyslog.d/haproxy.conf 파일 생성 및 세팅
        $ModLoad imudp
        $UDPServerRun 514
        $template Haproxy, "%msg%\n"
        local0.=info -/var/log/haproxy/haproxy.log;Haproxy
        local0.notice -/var/log/haproxy/haproxy-status.log;Haproxy
        local0.* ~
      ```

      ```
        # rsyslog restart
        service rsyslog restart
      ```

  * 실행

    ```
    [root@localhost ~] # /etc/init.d/haproxy restart
    또는
    [root@localhost ~] # service haproxy start
    ```

  * 부팅시 자동실행 설정

    ```
    [root@localhost ~] # chkconfig haproxy on
    ```

  * 종료

    ```
    [root@localhost ~] # service haproxy stop
    ```

  * Status 확인
    - xxx.xxx.xxx.xxx/haproxy.stats 접속합니다.


## Keepalived
  * Active, Standby L4 서버 모두 동일하게 진행합니다. 설정파일 수정만 다르게 진행합니다.

  * Keepalived 설치 : 수동으로 설치
    - yum 을 이용한 설치 실패 : Ubuntu에서는 쉽게 깔았는데..
    - 수동으로 설치

    ```
    [root@ ~] # cd /usr/local/src
    [root@localhost src] # wget http://www.keepalived.org/software/keepalived-1.2.16.tar.gz
    [root@localhost src] # tar -zxvf keepalived-1.2.16.tar.gz
    ```
  * Kernel Headers 설치

    ```
    [root@localhost ~] # yum -y install kernel-headers kernel-devel
    ```
  * Keepalived 컴파일

    ```
    [root@localhost ~] # cd /usr/local/src/keepalived-1.2.16
    [root@localhost keepalived-1.2.16] # ./configure --with-kernel-dir=/lib/modules/$(uname -r)/build
    [root@localhost keepalived-1.2.16] # make && make install
    ```
  * Required Softlinks 설정

    ```
    [root@localhost ~] # cd /etc/sysconfig
    [root@localhost sysconfig] # ln -s /usr/local/etc/sysconfig/keepalived .
    [root@localhost sysconfig] # cd /etc/rc3.d/
    [root@localhost rc3.d] # ln -s /usr/local/etc/rc.d/init.d/keepalived S100keepalived
    [root@localhost rc3.d] # cd /etc/init.d/
    [root@localhost init.d] # ln -s /usr/local/etc/rc.d/init.d/keepalived .
    ```
  * Configuration
    - 외부 IP와 바인딩 되도록 OS설정 수정
      /etc/sysctl.conf 수정 : 아래 라인 추가
      vi /etc/sysctl.conf

      ```
      net.ipv4.ip_nonlocal_bind=1
      ```
      아래 명령어 수행

    ```
    [root@localhost ~] # sysctl -p
    ```
    - /usr/local/etc/keepalived/keepalived.conf 생성 혹은 변경 : Active(MASTER) L4
      vi /usr/local/etc/keepalived/keepalived.conf

    ```
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
    ```
    - Event가 발생하게되면 e-mail로 알림을 받게 됨. master, backup의 vrrp_instance 명칭을 다르게 세팅하지 않으면 상황파악 어렵습니다.
    - 설정파일(/usr/local/etc/keepalived/keepalived.conf) 수정 : Standby(BACKUP) L4
      아래 내용만 다르게 세팅합니다.

      ```
      vrrp_instance VI_2 {
        ...
        state BACKUP        
        priority 101        
        ...
      }
      ```
    - 설정파일(/usr/local/etc/sysconfig/keepalived) 수정
      keepalived 실행시 입력되는 파라미터 값
      P 옵션은 VRRP 만 이용하겠다는 의미
      f 옵션은 configuration 위치 지정
      HAProxy와 함께 사용하면서 keepalived는 VRRP 기능만 이용할 것이라 P옵션을 사용했습니다.
      수동으로 설치하면서 configuration 위치를 찾지 못하는 현상이 있어서 직접 위치를 입력합니다.

      ```
      # 변경 전
      KEEPALIVED_OPTION="-D"

      # 변경 후
      KEEPALIVED_OPTION="-D -P -f /usr/local/etc/keepalived/keepalived.conf"
      ```

  * 실행

    ```
    [root@localhost ~] # /etc/init.d/keepalived start
    또는
    [root@localhost ~] # service keepalived start
    ```

   - 아래와 같은 메시지로 실패시
     /etc/init.d/keepalived 파일 수정필요합니다.
     vi /etc/init.d/keepalived

     ```
      Starting keepalived: /bin/bash: keepalived: command not found
     ```


     ```
      # 변경 전
      start() {
        echo -n $”Starting $prog: ”
        daemon keepalived ${KEEPALIVED_OPTIONS}

      # 변경 후
      start() {
        echo -n $”Starting $prog: ”
        daemon /usr/local/sbin/keepalived ${KEEPALIVED_OPTIONS}
     ```

  * 부팅시 자동실행 설정

    ```
    [root@localhost ~] # chkconfig keepalived on
    ```

  * 종료

    ```
    [root@localhost ~] # service keepalived stop
    ```

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
