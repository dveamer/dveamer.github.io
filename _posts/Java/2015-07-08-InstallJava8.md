---
layout: post
title:  "Install Java 8 On Ubuntu"
date:   2015-07-08 20:00:00
categories: java Ubuntu
---

![JAVA8](https://eclipse.org/xtend/images/java8_logo.png)

  * Ubuntu 14.04.2 LTS 64bit

### Install JDK
``` ubuntu
sudo apt-get install openjdk-8-jre
```

### Set JAVA_HOME
  * /etc/profile 파일의 맨 아래 라인에 JAVA_HOME, PATH 설정을 추가
    - Shift + G : 맨 아래로 이동 ( VI 단축키 )

``` ubuntu
sudo vi /etc/profile
```
``` vi
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
PATH=$JAVA_HOME/bin:$PATH
```
  * Log out & Log in

### Check
``` terminal
java -version
```