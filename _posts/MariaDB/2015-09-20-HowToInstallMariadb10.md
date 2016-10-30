---
layout: post
title:  "How to install MariaDB 10.01 on Ubuntu 14.04 LTS"
date:   2015-09-20 15:35:00
categories: Database
tags: MariaDB Ubuntu
---

![mariadb10.1](https://downloads.mariadb.org/static/generated/images/v2/ice_logo-5dcea9e47b780ff52f75c3c3304d54827f56211e.png)

( 이미지 출처 : [https://downloads.mariadb.org](https://downloads.mariadb.org) )

mariadb 5.5 가 ubuntu 기본 apt 패키지로 세팅이 되어있어서 저장소를 업데이트 후 새로 설치해야합니다.

근데 좀 편하게 가려고 구글링해서 다른 사람들의 글을 보고 시도했더니 자꾸 실패하더군요.  
원인은 ubuntu version 마다 다른 저장소를 가지고 있기 때문이었습니다.

결국 공식 사이트에서 내 ubuntu version에 맞는 설치가이드 문서를 제공받아서 설치 성공했습니다.

기존에 mariadb 5.5 를 사용 중이었는데   
그 당시의 계정, database, table 모두 유지되서 설치되네요.  

<!--more-->

# Install

## Check the version of Ubuntu

~~~
$ lsb_release -a

result >
  No LSB modules are available.
  Distributor ID:	Ubuntu
  Description:	Ubuntu 14.04.3 LTS
  Release:	14.04
  Codename:	trusty
~~~

## References
  * [mariadb 공식 가이드문서](https://downloads.mariadb.org/mariadb/repositories/#mirror=kaist&distro=Ubuntu)에서 ubuntu version에 맞는 가이드 검색 후 참고

