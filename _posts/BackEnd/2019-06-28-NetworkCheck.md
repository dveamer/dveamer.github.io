---
layout: post
title: "네트워크 연결여부 테스트 - 성공, 실패 케이스 기록"
date: 2019-06-28 00:00:00
categories: BackEnd
tags: BackEnd Network
---

시스템을 연동을 하기 전에 항상 두 시스템간의 네트워크 연결에 대해 체크가 필요합니다.  
간단하게 출발지 OS에서 telnet 명령어로 연결여부로 체크할 수 있습니다.  

하지만 프로토콜에 따라 성공 응답도 다르고 상황에 따라 실패 응답도 다양하기 때문에  
잠시 혼란을 가지게 되는 경우가 종종 있습니다.  

특히 시스템 운영 중에 문제가 생겼고 네트워크가 원인이라고 의심이 들 때 잘 못 판단할 가능성이 굉장히 높아집니다.  
그래서 케이스별로 기록을 시작해봅니다.  

<!--more-->

원인이 될수 있는 사항은 라우팅, 네트워크 구조, VIP(Virtual IP), NAT, DNS, 방화벽 등으로 굉장히 많습니다.  

눈감고 코끼리 다리를 만지며 코끼리의 모습을 설명하는 꼴이 되겠지만  
네트워크의 정상유무 확인을 목적으로 기록합니다.  

# Success Cases

모두 성공 응답을 짧은 시간내에 받았습니다.  

## HTTP Server

### Telnet

~~~terminal
$ telnet 192.168.0.20:8200
Trying 192.168.0.20...
Connected to 192.168.0.20.
Escape character is '^]'.
~~~

### cURL

~~~terminal
$ curl -I 192.168.0.20:8200
HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.6.7
Date: Thu, 27 Jun 2019 23:36:56 GMT
Content-type: text/html; charset=utf-8
Content-Length: 2039

~~~

## Socket Server

HTTP과 같은 표준 프로토콜이 아닌 자체 프로토콜을 사용하는 socket 통신의 경우 응답메시지가 일정치가 않습니다.  

Telnet과 cURL에 의한 테스트는 임시 방편적인 테스트가 되겠지만 empty reply(빈 문자열 응답) 혹은 알수없는 자체 프로토콜만의 메시지들이 출력된다면 연결은 성공적으로 됐다고 볼 수 있습니다.  


<!--ads-->

# Failure Cases

## Not Listening Port

IP(192.168.0.20)로 OS가 기동되어있으나 8404 port가 비활성화된 상태입니다.  

출발지 OS와 목적지 OS는 하나의 스위치에 연결된 상황에서 테스트 했습니다.  

짧은 시간내에 실패 응답을 받습니다.  

### Telnet

~~~terminal
$ telnet 192.168.0.20 8404
Trying 192.168.0.20...
telnet: Unable to connect to remote host: Connection refused
~~~

### cURL

~~~terminal
$ curl -i 192.168.0.20:8404
curl: (7) Failed to connect to 192.168.0.20 port 8404: Connection refused
~~~

## Not Listening Port Through VIP

작성 중..  

## Unassigned IP

할당되지 않은 IP를 호출할 경우입니다.  
즉, 스위치에 연결된 machine중에 192.168.0.40라는 IP를 할당받은 machine이 없는 경우입니다.  

3초 내에 실패 결과를 확인 했습니다.  

### Telnet

~~~terminal
$ telnet 192.168.0.40 8404
Trying 192.168.0.40...
telnet: Unable to connect to remote host: No route to host
~~~

### cURL

~~~termianl
$ curl -I 192.168.0.40:8404
curl: (7) Failed to connect to 192.168.0.40 port 8404: No route to host
~~~

## Wrong Domain Name

잘못된 도메인으로 호출 했을 때 얻는 결과 입니다.  
신규 도메인이라면 DNS에 아직 등록이 안되서 그럴 수도 있습니다.  

Public 네트워크 망에서는 빠른 실패 결과를 얻을 수 있으나 private망에서 DNS 서버의 구성상황에 따라 응답 시간이 다를 수 있습니다.  

만약 실패 응답이 늦게 떨어지는 상황이고 application의 connection timeout 설정이 짧게 잡혀있다면  
에러 로그는 connection timeout으로 둔갑되어있을 수 있습니다.  

### Telnet

~~~terminal
$ telnet dveamer1.com
telnet: could not resolve dveamer1.com/telnet: Name or service not known
~~~

### cURL

~~~terminal
$ curl -I dveamer1.com
curl: (6) Could not resolve host: dveamer1.com
~~~

<!--ads-->

## Shutdowned OS

IP(192.168.0.20)로 장비는 연결되어있으나 OS가 기동되어 있는 않은 상황입니다.  

출발지 OS와 목적지 OS는 하나의 스위치에 연결된 상황에서 테스트 했습니다.  

성공적으로 연결되는 것을 확인하고 OS를 기동 중지 후 테스트 했습니다.  

최초 1회는 3~10초정도의 대기시간을 갖다가 실패 결과를 확인했습니다.  
그 이후 부터는 3초 내에 실패 결과를 확인 했습니다.  

### Telnet

~~~terminal
$ telnet 192.168.0.20 8404
Trying 192.168.0.20...
telnet: Unable to connect to remote host: No route to host
~~~

### cURL

~~~terminal
$ curl -i 192.168.0.20:8404
curl: (7) Failed to connect to 192.168.0.20 port 8404: No route to host
~~~


## Shutdowned OS Through VIP

작성 중..  

## Failed To Recieve Response Packets

응답 패킷을 수신을 실패했을 때가 있습니다. (요청 패킷 전달여부는 알 수 없습니다.)  

예를들어 VIP를 호출 했는데 RIP로부터 응답 패킷이 오게 된다면 출발지 입장에서는 엉뚱한 packet이 온 것밖에 안되기 때문에 응답 수신을 실패하는 결과를 갖습니다.  

  * SNAT(Source Network Address Translation) 필요한 상황에서 미설정
  * DSR(Direct Server Return) 구조에서 목적지 OS의 loopback IP를 VIP로 변경하는 설정이 누락

긴 시간이 지나야 실패 결과를 확인할 수 있습니다.  
스위치, L4에서는 할일을 제대로 했기 때문에 출발지 OS의 conneciton timeout 설정에 의해 시간이 결정될 것입니다.  

### Telnet

~~~terminal
$ telnet 192.168.0.30 8200
Trying 192.168.0.20...
telnet connect to address [192.168.0.30](http://192.168.0.30) : Connection timed out
~~~

### cURL

~~~terminal
$ curl -i 192.168.0.30:8200
curl :(7) Failed connect to [192.168.0.30:8200](http://192.168.0.30:8200) : Connection timed out
~~~

#### References

  * https://www.ahnlab.com/kr/site/securityinfo/secunews/secuNewsView.do?menu_dist=2&cmd=print&seq=17002
  * https://blog.pages.kr/88

## Blocked By Firewall

출발지와 목적지 사이에 방화벽이 존재하고 해당 port에 대해 방화벽에서 차단을 했을 경우입니다.  

### Telnet

~~~terminal
$ telnet 192.168.0.20 8404
Trying 192.168.0.20...
telnet: Unable to connect to remote host: Connection refused
~~~

### cURL

~~~terminal
$ curl -i 192.168.0.20:8404
curl: (7) Failed to connect to 192.168.0.20 port 8404: Connection refused
~~~








