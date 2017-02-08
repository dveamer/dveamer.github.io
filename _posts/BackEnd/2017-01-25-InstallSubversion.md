---
layout: post
title:  "SVN(Subversion) 설정하기"
date:   2017-01-25 12:00:00 
categories: Programming
tags: subversion svn 
---

SVN server를 설치하고 repository를 생성, 설정
하는 방법에 대해서 알려드립니다.  
백업, 복원하는 방법은 다음 글을 통해서 알려드리겠습니다.  
 
<!--more-->


# Environment

  - Ubuntu 14.14 LTS  

# Install SVN

~~~terminal
$ sudo apt-get install subversion
~~~


# Set Up Repositories  

## Create Repositories

임의의 위치에 ```Repositories``` 디렉토리를 생성합니다.  
저는 ```/home/my```디렉토리 아래에 생성한다고 예를 들겠습니다.  

그리고 ```/home/my/Repositories``` 디렉토리 아래에 여러개의 repository를 만들어보겠습니다.  
일단 한 개만 만들고 필요할때마다 추가적으로 만드시면 됩니다.  

~~~terminal
$ mkdir /home/my/Repositories
$ cd /home/my/Repositories
$ svnadmin create repos1
$ svnadmin create repos2
$ svnadmin create repos3
~~~

## Configure the Repository  

### {Repository}/conf/authz  
.

### {Repository}/conf/passwd  
.

### {Repository}/conf/svnserve.conf  
.

# Set Up a SVN Daemon

## Start a SVN Daemon

svn client들이 repositories로 접속하도록 하려면 SVN daemon을 구동시켜야합니다.  

Repositories 디렉토리를 생성하고 해당 디렉토리를 바라보는 SVN daemon을 구동시킵니다.  
저는 ```/home/my```디렉토리 아래에 생성한다고 예를 들겠습니다.  

~~~terminal
$ mkdir /home/my/Repositories
$ svnserve -d -r /home/my/Repositories
~~~

## Starting SVN Server at System Boot

컴퓨터를 부팅할 때마다 SVN server가 자동으로 시작되도록 설정합니다.  

```/etc/init.d``` 디렉토리에 svn server 구동 script를 작성합니다.  
```/repositories/directory``` 위치를 수정해서 입력하세요.  

~~~terminal
$ cd /etc/init.d/ 
$ sudo touch svnserve
$ sudo echo 'svnserve -d -r /home/my/Repositories' > svnserve
~~~

실행권한을 줍니다.  

~~~terminal
$ sudo chmod +x svnserve
~~~

svn daemon 구동 script를 boot sequence에 등록합니다.  

~~~terminal
$ sudo update-rc.d svnserve defaults
~~~

# Access the Repository with the SVN / SVN+SSH Protocol

## with the SVN Protocol

## with the SVN+SSH Protocol


