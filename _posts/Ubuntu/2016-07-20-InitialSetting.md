---
layout: post
title:  "Ubuntu 설치 후 초기세팅"
date:   2016-07-19 12:00:00
lastmod: 2017-09-09 12:00:00
categories: Ubuntu
tags: Ubuntu Setting
---

Ubuntu 재설치 후 제가 개인적으로 필요로하는 프로그램들을 기록합니다.  

<!--more-->

# Subversion
  * [복원하기](/backend/SettingSubversion.html)

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

# RabbitVCS

~~~
$ sudo apt-get update
$ sudo apt-get install rabbitvcs-nautilus3
$ chown -R $USER:$USER ~/.config/rabbitvcs
~~~

# Git  

~~~
$ sudo apt-get git
$ git config --global user.email "your-email-address@example.com"
$ git config --global user.name "Your Name"
~~~
 
  
# fzf 설치

~~~
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf
~/.fzf/install
~~~

## fzf preview 

fzf를 이용해서 파일을 찾을 떄 파일 내용에 대한 미리보기를 제공하는 방법입니다.  

**~/.bashrc** 혹은 **~/.zshrc**을 열어서 아래내용을 추가합니다.  

~~~terminal
function fzfpw() {
  fzf --preview '[[ $(file --mime {}) =~ binary ]] &&
                 echo {} is a binary file ||
                 (highlight -O ansi -l {} ||
                  coderay {} ||
                  rougify {} ||
                  cat {}) 2> /dev/null | head -500'
}
~~~

터미널을 다시 열고 **fzfpw** 라는 명령어를 쳐보시면 미리보기가 제공되는 것을 확인할 수 있습니다.  

#### References

 * [fzf preview](https://seungdols.tistory.com/749)

# fav-dir 설치

최신 [fav-dir](https://github.com/johngrib/fav-dir/tree/master) 은 homebrew를 이용한 설치 방법이 가이드 되어있습니다.  
Ubuntu에서 linuxbrew 사용 없이 직접 설치 방법은 아래와 같습니다.  

~~~
git clone https://github.com/johngrib/fav-dir.git
cd fav-dir
mkdir -p ~/.local/bin/
cp ./fav-dir.sh ~/.local/bin/
~~~

그 후 **~/.bashrc** 혹은 **~/.zshrc**을 열어서 아래내용을 추가합니다.  

~~~bash
[ -f ~/.local/bin/fav-dir.sh ] && source ~/.local/bin/fav-dir.sh
~~~

# AWS

## Install Python PIP

~~~terminal
$ sudo apt-get install python3-pip
~~~

## Install AWS Client

~~~terminal
$ pip3 install awscli --upgrade --user
$ aws configure
~~~


