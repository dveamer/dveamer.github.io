---
layout: post
title:  "Install Ctrix Clietn On Ubuntu 14.04 LTS"
date:   2015-12-09 01:00:00
categories: Ubuntu Ctrix
---

![ctrix](https://lh3.ggpht.com/l6-eLEJbx230dQt3vt-02yMM-gVOcbfUC50uezBUICGSvBei85d-EPsPqDRUsNYySbo=w300-rw) ![ctrix](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2e/Citrix.svg/220px-Citrix.svg.png)

회사 클라우드 컴퓨터에 접속하기 위해서는 Ctrix Receiver가 필요하다.
모든 데이터가 클라우드 컴퓨터에 보관되고 작업 환경이 구성되어 있기 때문에 
사무실에서도 로컬 컴퓨터로는 할 수 있는 것이 없고 일단 Ctrix 로 클라우드 컴퓨터에 접속을 해야만한다.

지금까지 단 한번도 우분투에서 접속을 시도해본 적이 없었는데 
갑자기 회사 로컬 컴퓨터를 우분투로 꾸며보고 싶다는 생각이 들어서 시도해보게 됐다. 
일단 집에서 테스트겸으로 시도해봤는데 접속이 잘 되네..

<!--more-->

## Test Environment
  * Ubuntu 14.04 LTS
  * 64-bit

## Install

### Download the Citrix Receiver for Linux .deb package
  * [Download Ctrix Receiver](https://www.citrix.com/downloads/citrix-receiver/legacy-receiver-for-linux/receiver-for-linux-13-2.html) 접속
  * 페이지 하단으로 스크롤해서 64-bit, deb 확장자 파일 다운로드

  ![download64bit](/images/post_img/CtrixReceiver/CtrixReceiverDownload64bit.png) 

### Enable i386 Multiarch (64-bit only) 
  * 64-bit OS 사용자에게만 해당된다.
  * 64-bit Ctrix Receiver는 32-bit의 몇가지 패키지들과 의존관계가 있다고 한다.
  * 해당 패키지들을 이용하기 위해서 아래와 같이 설정하는 것으로 보여진다.

```
  sudo dpkg --add-architecture i386
  sudo apt-get update
``` 

### Install the downloaded package(s) and dependencies
  * 다운받아 둔 파일을 가지고 설치를 진행한다.	

```
  sudo dpkg -i ~/Downloads/icaclient_*.deb ctxusb_*.deb
  sudo apt-get -f install
```

### Add more SSL certificates
  * SSL 인증서가 필요한 모양인데 mozilla 에서 제공하는 인증서를 빌려쓰려는 의도로 보인다.

```
  sudo ln -s /usr/share/ca-certificates/mozilla/* /opt/Citrix/ICAClient/keystore/cacerts/
  sudo c_rehash /opt/Citrix/ICAClient/keystore/cacerts/
```

### Configure Citrix Receiver
  * Configuration Manager 를 실행시킨다. 

```
  /opt/Citrix/ICAClient/util/configmgr &
```

  * Ctrix 와 공유할 로컬 컴퓨터의 폴더를 지정한다.
  * File access tab > add > save

  ![configuration](/images/post_img/CtrixReceiver/CtrixReceiverConfiguration.png)

### Configure Chrome/Chromium
  * 아래와 같이 실행해주고나면 크롬으로 접속이 가능합니다.

```
  sudo xdg-mime default wfica.desktop application/x-ica
```

  * 이 단계까지 진행했으면 크롬 브라우저로 접속이 되어야 합니다.
  * 만약 이 단계까지 진행했는데 접속이 안된다면 아래 내용을 진행해보시기 바랍니다.
  * 아래의 내용은 Firefox 브라우저에 대한 설정 내용이라 크롬과는 상관없지만 저는 실수로 아래 내용을 수행했고 안되시는 분들과 차이가 있다면 이 내용이 가장 유력한 후보일 것이기 때문에 기록해둡니다. ( 되돌린 다음 테스트해보면 되지만 귀차니즘이.. )

### Fix Firefox plugin installation (64-bit only) 
  * Firefox 로 접속할 때 필요한 설정들의 일부분입니다. 
  * Firefox 로 접속하기 위해서는 브라우저에서 설정이 추가적으로 필요합니다. ( Reference 참고 )
  
```
   sudo mv /usr/lib/mozilla/plugins/npwrapper.npica.so /usr/lib/mozilla/plugins/npwrapper.npica.so_bak
   sudo mv /usr/lib/firefox/plugins/npwrapper.npica.so /usr/lib/firefox/plugins/npwrapper.npica.so_bak
   sudo mv /usr/lib/mozilla/plugins/npica.so /usr/lib/mozilla/plugins/npica.so_bak
   
   sudo ln -s /opt/Citrix/ICAClient/npica.so /usr/lib/mozilla/plugins/npica.so
   sudo ln -s /opt/Citrix/ICAClient/npica.so /usr/lib/firefox-addons/plugins/npica.so
```

## References
 * https://help.ubuntu.com/community/CitrixICAClientHowTo