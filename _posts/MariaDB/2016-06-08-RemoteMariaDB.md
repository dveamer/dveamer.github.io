---
layout: post
title:  "MariaDB 외부접속 설정 & 계정 생성"
date:   2016-06-08 12:00:00
categories: Database
tags: MariaDB
---

![mariadb10.1](https://downloads.mariadb.org/static/generated/images/v2/ice_logo-5dcea9e47b780ff52f75c3c3304d54827f56211e.png)  
( 이미지 출처 : [https://downloads.mariadb.org](https://downloads.mariadb.org) )

MariaDB 외부접속을 하기 위해  
어떠한 설정작업들이 있는지 확인해보고 작업방법에 대해서 간략하게 알아보자.  

<!--more-->

<br>

매번 세팅을 하면서 느끼는 것이지만..  

기록은 중요하다.  
특히, 세팅작업처럼 간혹가다가 발생하는 작업은 기록이 필수다.  

DB 초기 세팅 및 외부접속 허용은 몇번이나 해봤던 작업인데  
오늘도 한가지 작업을 까먹고 30분 가량을 헤맸다.  

# 방화벽 설정

MariaDB port (default port : 3306) 에 대한 inbound 설정을 한다.  

# MariaDB 설정

## /etc/mysql/my.cnf 설정 변경

```bind_address``` 라인을 주석처리하거나 0.0.0.0 으로 세팅한다.  

~~~
$ sudo vi /etc/mysql/my.cnf 

...

#bind_address=127.0.0.1 # 주석처리

...
~~~

## MariaDB 재기동

~~~
$ sudo service mysql restart
~~~


# 계정생성 & 권한설정

외부 접속을 허용할 계정을 생성하고 권한을 준다.  

## root 계정으로 MariaDB 접속  

~~~
$ mysql -u root -p
~~~

## 계정 생성 

~~~mariadb
/* 내부 접속용 */
create user 'RemoteUser'@'localhost' identified by 'localPassword';

/* 모든IP 외부접속용 */
create user 'RemoteUser'@'%' identified by 'remotePassword';

/* 특정IP 외부접속용 */
create user 'RemoteUser'@'192.168.2.%' identified by 'remotePassword';
~~~

  * 내부와 외부 접속용 계정의 비밀번호는 다르게 세팅하는 것이 좋다.  
    - 권한이 같으면 큰 의미 없다.  
    - root 계정과는 무조건 다르게 세팅하자.  

## 계정 생성 & 권한설정

~~~mariadb
/* 내부 접속용 */
grant all privileges on DB_NAME.* to RemoteUser@'localhost';

/* 모든IP 외부접속용 */
grant all privileges on DB_NAME.* to RemotUser@'localhost';

/* 특정IP 외부접속용 */
grant all privileges on DB_NAME.* to RemoteUser@'192.168.2.%';
~~~

  * 내부와 외부 접속용 계정의 권한은 다르게 세팅하는 것이 좋다.  
  * 방화벽 설정과 마찬가지로 외부접속 IP가 고정된다면 특정지어 세팅하는 것이 좋다.  

