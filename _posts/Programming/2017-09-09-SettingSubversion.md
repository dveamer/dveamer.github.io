---
layout: post
title:  "How To Set Up SVN(Subversion) Repositories"
date:   2017-09-09 12:00:00
categories: Programming
tags: Ubuntu SVN Setting
---

![Subversion Logo](https://upload.wikimedia.org/wikipedia/en/thumb/9/9f/Subversion_Logo.svg/1280px-Subversion_Logo.svg.png){:class="imgTitle"}  
( 이미지 출처 : [Wikipedia](https://upload.wikimedia.org) )  

저의 경우,  
Ubuntu를 새로 설치 할 때마다 해야하는 작업 중 하나가 SVN(Subversion) 세팅입니다.  
평소 백업해둔 repositories 덤프 파일을 가지고 전체 복원하는 과정을 기록해봅니다.  

기존 상태를 복원하는 관점으로 기록하므로  
revision 구간별 복원, 계정권한 설정 등과 같은 상세한 내용은 다루지 않습니다.  

<!--more-->


## Installing SVN

~~~terminal
$ sudo apt-get install subversion
~~~


## Loading A Dump File To New Repository

~~~terminal
$ cd /data/Repositories
$ svnadmin create Repository_Dir
$ svnadmin load Repository_Dir --force-uuid < Repository.dump 
~~~

```--force-uuid``` 옵션은 revision 정보를 기존과 동일하게 유지시켜줍니다.  
그 결과 접속 계정, 권한정보만 동일하다면 client에서 새로 설정할 필요가 없습니다.  

만약 백업해둔 덤프파일이 없다면  
로딩과정은 생략하고 Repository를 생성만 하면됩니다.  

## Setting SVN Accounts & Authorization

~~~terminal
$ vi /data/Repositories/Repository_Dir/conf/passwd
$ vi /data/Repositories/Repository_Dir/conf/svnserve.conf
$ vi /data/Repositories/Repository_Dir/conf/authz
~~~

계정과 권한 정보를 설정합니다.  

## Starting SVN at Boot

~~~teminal
$ cd /etc/init.d/ 
$ sudo echo "svnserve -d -r /data/Repositories" > svnserve
$ sudo chmod +x svnserve
$ sudo update-rc.d svnserve defaults
~~~

## Making A Dump File From Repository

~~~terminal
$ svnadmin dump /data/Repositories/Repository_Dir > /data/Backup/Repositoriy.dump
~~~

보통은 주기적으로 백업을 해야할텐데  
그건 스크립트로 작성해서 주기적으로 덤프파일을 만든 후 다른 저장공간에 복사를 해야겠죠.  


