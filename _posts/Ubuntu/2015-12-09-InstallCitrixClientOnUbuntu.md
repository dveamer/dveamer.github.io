---
layout: post
title:  "Install Citrix Receiver On Ubuntu"
date:   2015-12-09 12:00:00
categories: Ubuntu-Tools
tags: Citrix 
identifier: 20151209-485155446
---

![citrix](http://docs.citrix.com/content/dam/docs/en-us/legacy-edocs/receiver-windows-43/receiver-x1-icon.png) ![citrix](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2e/Citrix.svg/220px-Citrix.svg.png){:class="imgTitle"}

( 이미지 출처 : [http://docs.citrix.com](http://docs.citrix.com), [https://upload.wikimedia.org](https://upload.wikimedia.org) )

제가 다니는 회사의 클라우드 컴퓨터에 접속하기 위해서는 Citrix Receiver가 필요합니다.  
모든 데이터가 클라우드 컴퓨터에 보관되고 작업 환경이 구성되어 있기 때문에   
사무실에서도 로컬 컴퓨터로는 할 수 있는 것이 없고 일단 Citrix 로 클라우드 컴퓨터에 접속을 해야만하죠.

지금까지 단 한번도 우분투에서 접속을 시도해본 적이 없었는데   
갑자기 회사 로컬 컴퓨터를 우분투로 꾸며보고 싶다는 생각이 들어서 시도해보기로 했습니다.  
일단 집에서 테스트겸으로 시도해봤는데 접속이 잘 되네요.

<!--more-->

# Test Environment
  * Ubuntu 14.04 LTS
  * 64-bit

# Install

## Download the Citrix Receiver for Linux .deb package

  * [Citrix Receiver Download Page](https://www.citrix.com/downloads/citrix-receiver/legacy-receiver-for-linux/receiver-for-linux-13-2.html) 접속합니다.
  * 페이지 하단으로 스크롤해서 64-bit, deb 확장자 파일을 다운로드 합니다.

  ![download64bit](/images/post_img/CtrixReceiver/CtrixReceiverDownload64bit.png) 

## Enable i386 Multiarch (64-bit only) 

  * 64-bit OS 사용자에게만 해당됩니다.
  * 64-bit Citrix Receiver는 32-bit의 몇가지 패키지들과 의존관계가 있다고 합니다.
  * 해당 패키지들을 이용하기 위해서 아래와 같이 설정하는 것으로 보여집니다.

~~~terminal
$ sudo dpkg --add-architecture i386
$ sudo apt-get update
~~~ 

## Install the downloaded package(s) and dependencies

다운받아 둔 파일을 가지고 설치를 진행합니다.  


~~~terminal
$ sudo dpkg -i ~/Downloads/icaclient_*.deb ctxusb_*.deb

(Reading database ... 170848 files and directories currently installed.)
Preparing to unpack .../icaclient_13.2.0.322243_amd64.deb ...
Unpacking icaclient (13.2.0.322243) over (13.2.0.322243) ...
dpkg: error processing archive ctxusb_*.deb (--install):
 cannot access archive: No such file or directory
dpkg: dependency problems prevent configuration of icaclient:
 icaclient depends on libxerces-c3.1; however:
  Package libxerces-c3.1 is not installed.
 icaclient depends on libwebkit-1.0-2 | libwebkitgtk-1.0-0; however:
  Package libwebkit-1.0-2 is not installed.
  Package libwebkitgtk-1.0-0 is not installed.

dpkg: error processing package icaclient (--install):
 dependency problems - leaving unconfigured
Processing triggers for desktop-file-utils (0.22-1ubuntu1.1) ...
Processing triggers for gnome-menus (3.10.1-0ubuntu2) ...
Processing triggers for bamfdaemon (0.5.1+14.04.20140409-0ubuntu1) ...
Rebuilding /usr/share/applications/bamf-2.index...
Processing triggers for mime-support (3.54ubuntu1.1) ...
Errors were encountered while processing:
 ctxusb_*.deb
 icaclient
~~~

위의 명령어를 수행하면 3개의 필수 라이브러리가 설치되어 있지 않다고 에러메시지가 발생합니다.  
무시하시고 아래 명령어를 실행시키시면 에러난 라이브러리도 함께 설치해줍니다.  

~~~terminal
$ sudo apt-get -f install
~~~


## Add more SSL certificates

SSL 인증서가 필요한 모양인데 mozilla 에서 제공하는 인증서를 빌려쓰려는 의도로 보입니다.  

~~~
$ sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts/
$ sudo c_rehash /opt/Citrix/ICAClient/keystore/cacerts/
~~~

## Configure Citrix Receiver

Configuration Manager 를 실행시킵니다.  

~~~
$ /opt/Citrix/ICAClient/util/configmgr &
~~~

  * Citrix 와 공유할 로컬 컴퓨터의 폴더를 지정합니다.
  * File access tab > add > save

  ![configuration](/images/post_img/CtrixReceiver/CtrixReceiverConfiguration.png)

## Configure Chrome/Chromium

아래와 같이 실행해주고나면 크롬으로 접속이 가능합니다.  

~~~
$ sudo xdg-mime default wfica.desktop application/x-ica
~~~

  * 이 단계까지 진행했으면 크롬 브라우저로 접속이 되어야 합니다.
  * 만약 이 단계까지 진행했는데 접속이 안된다면 아래 내용을 진행해보시기 바랍니다.
  * 아래의 내용은 Firefox 브라우저에 대한 설정 내용이라 크롬과는 상관없지만 저는 실수로 아래 내용을 수행했고 안되시는 분들과 차이가 있다면 이 내용이 가장 유력한 후보일 것이기 때문에 기록해둡니다. ( 되돌린 다음 테스트해보면 되지만 귀차니즘이.. )

## Fix Firefox plugin installation (64-bit only) 

Firefox 로 접속할 때 필요한 설정들의 일부분입니다.  

~~~terminal
$ sudo mv /usr/lib/mozilla/plugins/npwrapper.npica.so /usr/lib/mozilla/plugins/npwrapper.npica.so_bak
$ sudo mv /usr/lib/firefox/plugins/npwrapper.npica.so /usr/lib/firefox/plugins/npwrapper.npica.so_bak
$ sudo mv /usr/lib/mozilla/plugins/npica.so /usr/lib/mozilla/plugins/npica.so_bak
   
$ sudo ln -s /opt/Citrix/ICAClient/npica.so /usr/lib/mozilla/plugins/npica.so
$ sudo ln -s /opt/Citrix/ICAClient/npica.so /usr/lib/firefox-addons/plugins/npica.so
~~~

위의 설정을 진행 후 브라우저 plug-in(add-on) 설정이 추가적으로 필요합니다. ( Reference 참고 )  
근데 2018.05.28 에 진행했을 때는 추가 설정없이 진행됐습니다.  

## How To Escape From Citrix View

Citrix에 전체화면으로 접속이 되서 로컬 PC 화면으로 빠져나오지 못하는 상황이 생깁니다.  
빠져나오고 싶으실때는 ```Ctrl+F2```를 누르신 후 ```Alt+Tab```을 누르시면 됩니다.  


## References
 * https://help.ubuntu.com/community/CitrixICAClientHowTo
