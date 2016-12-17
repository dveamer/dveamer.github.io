---
layout: post
title:  "모바일 앱 테스트 팁 - 운영, 개발 서버 스위칭"
date:   2016-12-04 12:00:00
categories: Android
tags: Test
---

집에서 모바일 개발을 시작하시는 분들께  
앱 변경 없이 운영서버와 개발서버를 선택해서 접속하는 팁을 공유드립니다.  

Domain을 이용해서 접속하되 집에서는 개인 nameserver를 이용해서 개발서버로 접속하게 하는 방법입니다.  

dnsmasq라는 프로그램을 이용해서 nameserver를 대체했습니다.  
실제로 nameserver의 모든 기능을 갖추지는 못하겠지만 제 목적을 달성하기에는 충분한 프로그램입니다.  
설치나 사용법은 굉장히 간단합니다.  

<!--more-->

nameserver 선택을 위해서는 집의 WIFI AP(예:ipTIME) 혹은 DNS changer와 같은 앱을 이용합니다.  

안드로이드 앱, ipTIME, Ubuntu OS를 가지고 진행했지만  
아이폰도 당연히 가능할 것으로 생각되고  
다른 WIFI AP, OS도 모두 대체제를 가지고 있을 것으로 생각됩니다.  

# 어떤 상황에서 유용할까?

Google Play에 앱을 올리고나면 실제 사용자들이 서버에 접속합니다.   
아직 접속자가 거의 없다하더라도  
제대로 된 테스트 없이 소스를 반영하거나  
서버를 수시로 재기동하는 것은 좀 껄끄럽습니다.  

결론은 개발서버에서의 테스트 입니다.  
( 1인 개발이라 개발, 운영 2단계로 진행합니다. )  

웹서비스라면  
API testing tool (예:Postman) 이나 브라우저에서  
개발서버의 도메인 혹은 IP를 기입해서 테스트하면 됩니다.  
테스트가 끝나면 그 소스 그대로 운영서버로 반영하면 되죠.  

더 좋은 방법은 PC의 hosts 파일과 도메인 주소를 활용하는 방법입니다.  

그러면 모바일 앱에서는 어떤 방법들이 있을까요?  

# 다른 해결방법

## 매번 소스 수정

참사를 불러올 수 있습니다.  
Google Play 올렸는데 "헉..! 개발서버 주소로 올렸다.. OTL"  
라는 생각이 든다면 이미 망한겁니다.  
재등록해도 구글의 심사를 받는데 시간이 걸리죠....  

게다가 테스트를 끝낸 상태에서 소스를 다시 수정해야한다는 점이 굉장히 마음에 들지 않습니다.  

## android 의 hosts 파일 수정  

android 의 hosts 파일을 수정하려면 root 권한이 필요하죠.  
제조사에서 루트권한을 막아놨기 때문에 루팅이 필요합니다. 귀찮죠?  
게다가 루팅이라니.. 보안이 신경쓰입니다.  

## android:debuggable 정보를 이용한 방법  

build 시에 IDE가 true/false 로 바꿔준 값을 활용해 동적으로 처리 가능합니다.  
보유한 모바일이 많다면 이 방법으로 개발/운영 용을 구분해서 사용하는 것이 좋을 것 같습니다.  
다만, 보유한 모바일이 적다면 매번 빌드하셔야 합니다.  

그리고 앱과 서버가 동시에 버전업이 된 경우,  
기존 버전의 앱에서도 신규 버전의 서버와 연동이 문제없이 되는지 테스트해야합니다.  
그렇지 않으면 강제 업그레이드 처리를 하더라도 앱이 구글플레이 혹은 앱스토어에 등록되는 시각과 서버의 버전이 올라가는 시각이 동일하지 않으면 에러가 날 수 있겠죠.  

이런 작업을 하려면 형상관리 툴을 다시 뒤져서 기존 버전으로 재빌드하셔야 할 겁니다.  

# 도메인과 네임서버를 이용한 방법  

마지막으로 제가 고민해 본 방법입니다.  

집의 WIFI를 통해서 테스트 할 때는 개발서버로 접속되고  
통신사 망을 이용해서 할 때는 운영서버로 접속되기 때문에  
소스 변경이 필요없습니다.  

또한 기존버전 앱의 테스트가 필요하다면  
구글플레이, 앱스토어에 올라가있는 실제 운영버전의 앱으로 테스트를 진행하면 됩니다.  

단점은 nameserver, 개발서버 역할을 하는 2대의 컴퓨터가 필요하다는 점입니다.  
그리고 nameserver가 외부IP를 가지지 않는한 집밖에서는 개발서버에 접속하지 못합니다.  

## 개인 nameserver 설정

nameserver가 될 서버에 dnsmasq를 설치하고 설정합니다.  

### dnsmasq 설치 & 설정

~~~
$ sudo apt-get install dnsmasq
~~~

### /etc/hosts 설정

개발서버의 IP와 도메인명을 추가합니다.  

~~~
192.168.0.4     blinddate.dveamer.com
~~~

### /etc/dnsmasq.conf 수정

hosts 에서 목적지를 찾지 못했을 경우,  
재문의할 다른 네임서버 IP를 입력합니다.  

구굴 네임서버가 8.8.8.8 과 8.8.4.4 입니다.  

~~~
nameserver 8.8.8.8
~~~

하지만 이번 설정은 굳이 진행하지 않으셔도 됩니다.  
ipTime 혹은 DNS changer 설정에서 보조DNS서버 설정을 통해서도 처리 가능한 내요입니다.  

### dnsmasq 재시작

~~~
$ sudo service dnsmasq restart
~~~

## ipTIME 설정

### ipTIME 고정 IP 설정

```ipTIME 관리화면 > 고급설정 > 네트워크 관리 > 내부 네트워크 설정 > 수동 IP 할당 설정``` 으로 이동해서 개인 네임서버와 개발서버의 내부 고정 IP를 할당합니다.  

### ipTIME DNS 설정 

```ipTime 관리화면 > 고급설정 > 네트워크 관리 > 인터넷 연결 설정``` 으로 이동해서 ```수동으로 공유기의 DNS 서버 설정```을 체크합니다.  
```기본 DNS 서버```에 개인 네임서버의 IP를 입력해주시고 ```보조 DNS 서버```는 8.8.8.8 로 입력합니다.  

## 테스트

여기까지 테스트가 완료됐다면 1차 테스트를 진행해 볼 필요가 있습니다.  
테스트 방법은 단순합니다. WIFI 연결 후, 브라우저나 앱으로 도메인으로 개발서버에 접속을 시도하시면 됩니다.  

저의 경우는 blinddate.dveamer.com라는 도메인으로 접속을 시도했고 Windows 7 browser, iPhone 4 browser, Ubuntu 14.04 LTS browser에서 개발서버에 연결됐습니다.  

근데 안드로이드 Nexus 5에서는 실패했습니다.  

성공적으로 되셨다면, 집 WIFI를 이용할 때는 개발서버로 접속되고 통신사망 혹은 집 밖의 다른 WIFI를 이용하실 때는 운영서버로 접속되는 환경이 구성됐습니다.  

만약 사용하는 모바일이 테스트에 실패한다면 아래와 같이 접속할 namerserver를 선택해주는 앱을 이용하시면 됩니다.  

## Mobile Nameserver 선택 앱

nameserver를 선택가능하게 해주는 모바일 앱에서  
1차 DNS 정보를 위에서 제작한 개인 nameserver의 내부 IP로 입력합니다.  
2차 DNS 정보는 8.8.8.8로 입력합니다.  
그 후 start 시켜주시면 됩니다.  

![DNS Changer](https://lh3.googleusercontent.com/PSPBtHbSSTeVdqSVb15d2DnhbmCpPpv0vqW034UKwdGaYtiGtJ5l32uhHWpeTAJGlEU=w300)  
( 출처 : [DNS Changer](https://play.google.com/store/apps/details?id=com.burakgon.dnschanger&hl=ko) )

저는 [DNS Changer](https://play.google.com/store/apps/details?id=com.burakgon.dnschanger&hl=ko) 라는 앱을 이용했습니다.  
반드시 이 앱이 아니라 DNS를 변경할 수 있는 다른 앱들도 상관없습니다.  
루팅없이 앱 설치만으로 수정 가능합니다.  

집 WIFI에 연결 후, DNS Changer를 start 시켜주시면 개발서버로 접속됩니다.  
집 WIFI가 아닌 다른 망에 접속하거나 DNS Chager가 stop 상태라면 운영서버로 접속됩니다.  

