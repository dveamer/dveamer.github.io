---
layout: post
title:  "Ubuntu 설치 후 초기세팅"
date:   2016-07-19 12:00:00
categories: Ubuntu
tags: Setting
---

Ubuntu 재설치 후 제가 개인적으로 필요로하는 프로그램들을 기록합니다.  

<!--more-->

# Subversion

~~~
$ sudo apt-get install subversion
~~~

# Gradle

~~~
$ sudo add-apt-repository ppa:cwchien/gradle

$ sudo apt-get update

$ sudo apt-get install gradle
~~~

# Java 8
  * [설치하기](/java/HowToInstallJava8.html)

# Python 3

  * 이미 설치되어있으나 python3 라는 명령어로 접근해야합니다.
  * Soft-link를 변경해서 default를 임의로 python3로 변경하면 안됩니다. 
    이유는 python3로 정상적으로 돌아가지 않는 프로그램들이 많기 때문입니다. 
    우분투 소프트웨어 센터도 에러가 나고 우분투 업그레이드도 실패하는 경우가 발생합니다.

# MariaDB
  * [설치하기](/database/HowToInstallMariadb10.html)
  * [계정생성 등 초기 세팅](/database/RemoteMariaDB.html)


# Android SDK

  * https://www.davidlab.net/ko/tech/how-to-setup-android-dev-env-on-ubuntu-part1/
  * [공식사이트](https://developer.android.com/studio/index.html) 의 맨 아래로 내려가서 sdk 다운로드

  * java.io.IOException: Cannot run program "/home/mret/tools/android-sdk-linux/build-tools/22.0.1/aapt": error=2, 그런 파일이나 디렉터리가 없습니다  

~~~
$ sudo apt-get install lib32stdc++6 lib32z1
~~~

  * SDK Manager

# IntelliJ 

  * 모바일과 연결해서 android를 실행시키면 빌드는 성공하지만 배포하다가 실패가 일어납니다.  
    Android Studio에서는 잘됐었는데 IntelliJ에서는 문제가 발생해서 당황했었습니다.  
    ```File > Setting > Build, Execution, Deployment > Instant Run``` 에서  
    ```Enable Instant Run to hot swap code/resource change on deploy (default enable)``` 라고 적힌  
    첫번째 콤보박스를 해제하면 해결됩니다.  



