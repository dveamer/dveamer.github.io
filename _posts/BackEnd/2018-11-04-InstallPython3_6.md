---
layout: post
title: "Python3.6 설치"
date: 2018-10-07 00:00:00
categories: BackEnd
tags: BackEnd Setting Python
---

![Python](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f8/Python_logo_and_wordmark.svg/260px-Python_logo_and_wordmark.svg.png)  
( 이미지 출처 : [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)) )  

Ubuntu 14.04 LTS에서 Python3.6 을 컴파일 설치하는 법을 알아봅니다.  

<!--more-->

## 임시 디렉토리 생성

소스를 다운 받고 컴파일할 임시디렉토리를 생성합니다.  

~~~terminal
$ mkdir -p /tmp/intall_python3.6
~~~

## Python3.6 소스코드 다운로드

~~~terminal
$ cd /tmp/intall_python3.6
$ wget https://www.python.org/ftp/python/3.6.3/Python-3.6.3.tgz
$ tar -xvf Python-3.6.3.tgz
~~~

## 컴파일

~~~terminal
$ cd Python-3.6.3
$ sudo ./configure --enable-optimizations
$ sudo make -j8
$ sudo make install
~~~

세번째 명령어에서 10분정도의 꽤나 오랜시간이 걸리니 잠시 다른 작업을 하셔도 됩니다.  

## 테스트

~~~terminal
$ python3 --version
~~~

## 작업 완료 후 임시디렉토리 삭제 

~~~terminal
$ cd /tmp
$ rm -rf install_python3.6
~~~

#### References

  * [Install Python 3.6 on Ubuntu 14.04 and 16.04 LTS](http://devopspy.com/python/install-python-3-6-ubuntu-lts/)



