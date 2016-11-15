---
layout: post
title:  "How to install Java 8 On Ubuntu"
date:   2015-07-08 20:00:00
lastmod: 2016-07-19 12:00:00
categories: Java
tags: Java8 Ubuntu
---

![JAVA8](https://eclipse.org/xtend/images/java8_logo.png)

( 이미지 출처 : [https://eclipse.org](https://eclipse.org) )

기본적인 Java 설치 내용입니다.

Java 8 의 변화의 정말 일부분만 사용해봤지만  
Generic 객체선언과 Lambda 를 사용하는 부분은 코디하기가 정말 많이 편해졌습니다.  
HashMap 도 많은 데이터를 보관시에는 성능이 더 좋아졌다고 하고요.  

수많은 변화에 대한 공부는 아직 제대로 못했지만 일단 먼저 깔아보기나 합니다.  

<!--more-->

# Install JAVA 8

## Environment
  * Ubuntu 14.04.2 LTS 64bit

## Install JDK

~~~console
$ sudo add-apt-repository ppa:openjdk-r/ppa

$ sudo apt-get update

$ sudo apt-get install openjdk-8-jdk

$ sudo update-alternatives --config java

$ sudo update-alternatives --config javac
~~~

## Set JAVA_HOME
  * /etc/profile 파일의 맨 아래 라인에 JAVA_HOME, PATH 설정을 추가
    - Shift + G : 맨 아래로 이동 ( VI 단축키 )

~~~console
$ sudo vi /etc/profile
~~~

~~~bash 
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/jre/bin
PATH=$JAVA_HOME/bin:$PATH
~~~
  * Log out & Log in

## Check
    
~~~console
$ java -version
~~~

