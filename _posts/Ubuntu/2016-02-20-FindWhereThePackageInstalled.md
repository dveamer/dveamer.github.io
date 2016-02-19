---
layout: post
title:  "패키지가 설치 된 위치 찾기 in Ubuntu"
date:   2016-02-20 00:59:00 
categories: Ubuntu
tags: Package Install Directory Where
---

Windows 에서는 대부분 Program files 디렉토리에 설치가 되는 편인데  
Ubuntu 는 좀 이곳저곳에 설치되는 느낌이 강합니다.  

어떤 방법으로 설치하느냐에 따라서도 좀 달라지고  
게다가 어떤 계정으로 설치하느냐에 따라 달라지는 것 같기도 하고요.  

패키지가 설치 된 디렉토리 찾는법을 알아보겠습니다.

<!--more-->

# apt-get install 로 설치된 경우
  * dpkg -L <packagename>
  * apt-get install 로 설치한 패키지는 검색이 됩니다.

~~~
  dpkg -L openjdk-8-jre
~~~

# gem 으로 설치된 경우
  * gem environment

~~~
  gem environment | grep ruby
~~~

