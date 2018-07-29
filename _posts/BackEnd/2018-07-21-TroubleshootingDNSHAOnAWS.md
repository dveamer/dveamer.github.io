---
layout: post
title: "AWS를 사용하면서 겪은 트러블슈팅 (DNS 기반 HA 관련 이슈)"
date: 2018-07-21 00:00:00
categories: BackEnd
tags: Troubleshooting
---

![AWS 구성](https://lh3.googleusercontent.com/XjE7KO8Ls2asWJfXBLURKk80eEQzTko7AH97HJ7u5e4a6g-0047jf2uev1R6Qqo3Kl7TgTVtyQ3CfcbiXSVgwUbwP1zIN8GRkTOLLkeVI6o4ayy2cMH2fgQoVfixd0RAXDZrL8t5Jtn0kuPADwKkAYlzejaeYWglEY_mLU4nu6iAqpn0HJqKVz7c6bihsrknBoDhKQN5HARoYXhWKxeEhYHVV4obhmsdd2JuZXxHjvaqcuIgbL0XULkA4Zy5grDAzHC-Uap7O5pYo7K0MFQfPmi7GYXGoCCVgOnPczeTPlaikVZeVb0dEAAxRPNXaWCCqIkF1gf0P5auik7Gjgl4JAg9pwAW0biIPGfUAcC8HGnNGQu6k6N67mocjgV3NeiBOPmlO94pMfRkF2jqJMfHrh7hHPcSDso5RoWMGzmGyZmXjmYPhqDEzbLALSrhSW4wR-uJbMZjUerWKXxTCVX3cZDfLkBocqSQ-tyNxTCkBEW3jsH2YubQHSYRijs3Lis9tO4VceHQEh2Ehsiy46DoyuHkHuOFycxSmIh91QrqpO44ctwww4xkWPPgxe3a1s74rtrXQPQ0EhYG5DOvVC6Yk5q8BEUDH09zoCxO_qbz8IDlqGNkoK_DQ_Y6a53sOU1raEPnCNmbUVcc_BkPWK-Hfpj3F7V9HSiG=w616-h518-no)

AWS를 이용해서 프로젝트를 진행하면서 겪은 트러블슈팅 중  
DNS 기반 HA 구성과 관련 된 두가지 트러블슈팅에 대해서 공유합니다.  

Apache HTTPD와 ELB를 함께 사용하면서 주기적으로 14초정도의 응답지연건이 발생했던 건과  
Aurora DB fail-over에 걸리는 시간을 최대한 줄여봤지만 3초정도의 한계가 있었던 건에 대해서 공유하겠습니다.  

<!--more-->

2018년도 3월까지 해당 프로젝트를 수행했고  
글을 작성하는 지금은 7월이기 때문에 정확한 수치, 덤프 같은 자료를 갖고 있지 않습니다.  
기억에 의존해서 작성하는 글이다보니 수치에 대해서는 최소한으로 언급하겠습니다.  

# DNS 기반의 HA 구성

공유드리기로한 ELB, Aurora DB 관련 트러블슈팅의 공통점은  
AWS가 ELB와 Aurora DB의 HA(High Availability)를 확보하기 위해 DNS를 이용한다는 점입니다.  
( ELB, Aurora DB만이 아니라 AWS 위의 대부분의 인스턴스, 서비스 들은 서로 도메인으로 호출을 하고 DNS를 통해 HA를 확보하는 것으로 알고 있습니다. )  

그러나 이유로 DNS 기반의 HA 구성이 무엇인지에 대해서 먼저 설명합니다.  

## DNS 질의

Client 프로그램에서 dveamer.github.io 라는 도메인을 호출할 때 무슨 일이 일어나는지 살펴봅시다.  

![DNS질의](https://lh3.googleusercontent.com/iqK3q838DV5_LBqK0rtLAsI335iSjW0VFAaGYdqgODghnN9G-JSTCGcnvnAgy9H1b2RxbJPfUgv3Jl7Yx1VEvk077rnCwGABBMVI_ppWEaoWam9VnfSe4qQSW0aPJqbcnzZVZ5KJohjJZ94diBb0QyXeBn0WAMwIjl_HUFqXuqicglTFiSY8rOOQPefzkwJhuXbv7IlnlTZsoEOU2pQqeGbxpyrf5wUHttoZp6NMndhnT1-iAIxqn2S0-SsRa5SJxBW-KME_MvICWfing1gMKuEWxHdVV1WMVu7AQQAXl0bjUYLjKivETsEh3r23vtpzP3Ooa0gMDDB-vvt0MBnqPgF-WBMT5S58iuJmHgoVyQTW0H2nQsofS4F-WHIm9CKj3cP-J34n0mKC6widv87OrPl5OcpMaDRZW2k_mf9Lr0UXiLum7dDb2qsqFmUO_Dwe4ZN0zoFW1mFPwvOof38h0dkNwK33cTnpS3KSXlKCUOQIAnkAZNXgGFZHsKjT_YAQn8y0D3kqGRbTQ85AcQQM0HdgdyNV6TjULe35UQb85svz3Hyip4XOrCIIGloItgHh2_NGq2tXinl0Zq-265GeV7aRbMpb_S_kBNuc2GagVN2Kfwh9oY1bzjF9w82GcVGsd0J9mjzSRqeLQalNA44ftX1z0gaMFJTt=w667-h384-no)

  1. Client는 dveamer.github.io 이라는 목적지 server의 도메인 정보만 알고있는 상황입니다.
  2. Client의 OS는 DNS server에 질의하여 dveamer.github.io에 대한 IP 리스트 획득합니다.
  3. OS는 dveamer.github.io 도메인과 IP 리스트 정보를 local DNS에 일정시간동안 보관합니다.
  4. Client는 OS로부터 제공받은 IP 리스트 중 하나를 선택하여 호출합니다. 


DNS 기반의 HA 구성이란  
위에서 나온 DNS server의 도메인, IP 정보에 대한 관리를 통해 목적지 서버의 HA를 확보하는 것을 말합니다.  

### Server 장애에 대한 HA 확보

만약 server1과의 연결이 실패한다면 client는 server2와 연결을 시도하게 됩니다.  
Client에 설정된 connection timeout 만큼의 응답지연이 생길 수 있겠지만 결국 server2와 연결되고 HA가 확보 됩니다.  

이 글 전체의 주제를 설명하기 위한 여기서의 키포인트는  
client는 목적지 서버의 개수나, IP 정보를 모른채  
도메인(dveamer.github.io)만 알고있어도 목적지 서버에 접근이 가능하다는 점입니다.  

### DNS 기반 HA를 이용한 DR(Disaster Recovery) 구성 

Server3을 구성하고 DNS server에 목적지 server IP를 server3의 IP로 변경합니다.  

![DNS를 통한 DR구성](https://lh3.googleusercontent.com/wVelRAGXACvfbScY_FP-rLE8Wj-T-PgNkz3XreCX1LES2yvt4rPnD6HBxLlnHk4qLquLy6uUIMHVa8pCpG2tILNqNnYZXyawevxXTfDsDLKOVDwaJqVvu_VP3amhR5EMgt9sCMrZVBzc_Qa2PzEsqVFwWiEBC0woHU2HQU9FxvYUN1eSsdFZrR2IepcbALvBxa3Zz6yPZRA4QUiY0ezs4mXJV-kJimurTzUHZIR46NhOnMUFlk-J0y6C0gTnmre86lIfu3kfWnQLpJMDb6hAZc4WaRfbTP2fBQndxe6f10MTReOnbOqwYP27Dr9ws6rOg4cmkgO4ukmzoNl5zwGmhQL_PcauKc37LpYsjktKBzmYZ5nxXnI_KWBcVkGdjE8TDObgilbvhmSs_qTxnqM_BeMEnsBEJVTZaap9OFG_7hAxOlQN6l2-S3rO_RppXLEyCCRE4ljkmIWrQjO5yc_BaFw4Ejdef-4GvmWvOdw7usbcwVbWfQAd5ZLURVELUvzh1WrOiA5PPWE16_JkJ4FRpG3PVe5ox8WN7TkBBd27TcrdTZ6WiLJRX_Wnwbq6vcV2TsQmYiZoURqgQnE63mkTlhDLLP9GhBWVXLlnU_3YPCoNNV_k2KAjWxlNkm-ZhidJBi8GsOobWDkdTqsfcYCJxccDnjB2AYAW=w624-h526-no)

Client의 OS local DNS에 보관했던 정보가 만료가 되면 DNS server에 재 질의를 하게 됩니다.  
그 때 DNS server에서 192.168.30.3 IP를 제공한다면 client는 server3에 연결을 시도하게 됩니다.  

DNS server에 저장된 정보관리를 통해 server 1, 2로 연결되던 서비스를 server 3으로 연결되도록 처리했습니다.  

여기서는 client의 목적지를 하나의 server로 표현했지만  
각 IP 뒤에 server가 아닌 zone이 구성되어있다고 생각하시면 됩니다.  

이 글 전체의 주제를 설명하기 위한 여기서의 키포인트는  
목적지 서버를 교체하는 과정에서 client를 수정하는 과정없이  
DNS 정보변경만으로 client가 목적지 server3으로 접근하게 되었다는 점입니다.  

# Apache HTTPD 와 ELB

이제 본론으로 들어가서 첫번째 트러블슈팅 경험담입니다.  

![AWS 구성](https://lh3.googleusercontent.com/XjE7KO8Ls2asWJfXBLURKk80eEQzTko7AH97HJ7u5e4a6g-0047jf2uev1R6Qqo3Kl7TgTVtyQ3CfcbiXSVgwUbwP1zIN8GRkTOLLkeVI6o4ayy2cMH2fgQoVfixd0RAXDZrL8t5Jtn0kuPADwKkAYlzejaeYWglEY_mLU4nu6iAqpn0HJqKVz7c6bihsrknBoDhKQN5HARoYXhWKxeEhYHVV4obhmsdd2JuZXxHjvaqcuIgbL0XULkA4Zy5grDAzHC-Uap7O5pYo7K0MFQfPmi7GYXGoCCVgOnPczeTPlaikVZeVb0dEAAxRPNXaWCCqIkF1gf0P5auik7Gjgl4JAg9pwAW0biIPGfUAcC8HGnNGQu6k6N67mocjgV3NeiBOPmlO94pMfRkF2jqJMfHrh7hHPcSDso5RoWMGzmGyZmXjmYPhqDEzbLALSrhSW4wR-uJbMZjUerWKXxTCVX3cZDfLkBocqSQ-tyNxTCkBEW3jsH2YubQHSYRijs3Lis9tO4VceHQEh2Ehsiy46DoyuHkHuOFycxSmIh91QrqpO44ctwww4xkWPPgxe3a1s74rtrXQPQ0EhYG5DOvVC6Yk5q8BEUDH09zoCxO_qbz8IDlqGNkoK_DQ_Y6a53sOU1raEPnCNmbUVcc_BkPWK-Hfpj3F7V9HSiG=w616-h518-no)

위 그림과 같이 ELB-Web-ELB-WAS 형태로 구성고 Web server는 Apache HTTPD 를 사용했습니다.  
ELB는 CLB 또는 ALB를 사용했고 두 형태의 ELB는 IP가 변경되기 때문에 Apache HTTPD에는 ELB의 IP가 아닌 도메인을 설정해서 운영했습니다. 그리고 ELB가 AJP 프로토콜을 처리할 수 없기 때문에 Proxy-Pass 방식으로 연동했습니다.  

구축 기간에도 아무 문제 없었고 운영 오픈 이후에도 문제 없다가 어느날 갑자기 14초정도의 응답지연 건이 수시로 발생했습니다. Apache HTTPD의 access-log에는 14초정도의 응답지연건이 있었지만 Apache Tomcat의 access-log에는 응답지연 건이 없었습니다. 그리고 Apache HTTPD 를 재기동했더니 응답지연 건은 사라지고 재현되지 않았습니다.  

ELB 관련 로그 검토를 AWS 측에 요청하여 응답받은 몇가지 답변 중에 다음과 같은 내용이 있었습니다.  
ELB가 교체되어 IP가 바꼈는데 Apache HTTPD가 과거의 IP를 계속해서 호출한 케이스가 발견되었다고 합니다.  

일단 ELB가 교체되었다는 얘기는 ELB에 문제가 있어서 수동으로 교체했다는 얘기가 아닙니다.  
ELB는 scale in/out시에 IP가 추가/변경/삭제되며 scale in/out 없는 평상시에도 ELB 교체가 수시로 일어날 수 있습니다.  
ELB도 도메인을 갖고 있는데 ELB에 대한 변경사항이 생기면 AWS 글로벌 DNS격인 Router53에 그 정보가 반영되고  
Apache HTTPD는 일시적인 응답지연이 발생하더라도 ELB의 도메인에 대해서 DNS 질의를 통해 새로운 IP를 확보하고 정상적인 접속이 이뤄져야했는데 그렇지 않고 14초의 응답지연이 수시로 발생해버린 것입니다.  

## 원인 분석

Router53에 ELB 도메인을 계속해서 질의하며 ELB scale out시 Router53에 정보갱신이 빠르게 일어나는지도 체크해봤고  
Apache HTTPD의 로그 레벨을 Debug로 바꿔서도 확인하고 TCP dump 분석도 하고 
VPC에 구성해둔 DNS 서버와 Web Server의 local DNS의 TTL도 수정해서 테스트해보는 등 다양한 가설을 세우고 수많은 삽질을 해봤습니다.  

테스트도 굉장히 어려웠습니다.  
ELB의 IP를 변경시키기 위해 scale out을 시켰다가 scale in을 시키고 변화를 기다렸습니다.  
sacale in/out을 위해 부하를 걸면 다른 오류의 발생을 통해 테스트에 영향을 줄 수 있고 비용도 발생하기 때문에  
AWS에서 수동으로 scale in/out을 진행했으며 그 과정에서 대기하는 시간이 길어져서 테스트가 매우 힘들었습니다.  

최종적으로 얻은 결론은 우리가 모르고 있던 Apache HTTPD의 특성이었습니다.  

### Apache HTTPD 의 내부 DNS 관리

Apache HTTPD는 기동시에 자신이 사용해야할 IP를 수집합니다.  
그 과정에서 도메인으로 된 정보는 DNS에 요청해서 IP를 받고 보관하게 됩니다.  
그리고 다수의 worker process에게 IP정보를 제공합니다.  

처음에는 올바른 IP정보를 받았으니 worker process은 ELB를 호출하고 정상적인 응답을 받을 수 있었습니다.  
근데 ELB의 IP에 변화가 생기면 worker process는 연결에 실패하게 됩니다. 여기서 몇차례 재연결시도에 의해 14초의 시간이 소비됩니다.  
그러면 local DNS에 질의를 통해 최종적으로 Router53으로부터 올바른 IP정보를 받아옵니다.  
그 뒤 worker는 받아온 올바른 IP를 가지고 worker process는 ELB를 정상적으로 호출하게 됩니다.  

근데 Apache HTTPD의 main process는 주기적으로 worker process의 정보를 초기화 시킵니다.  
그 이유는 3회연속 접속실패된 IP는 worker process가 연동대상에서 제외하는 기능이 있습니다.  
근데 접속실패 되던 IP의 서버가 정상적으로 복구되었을 때 연동대상에서 제외된 IP를 복구 시키기 위한 방안입니다.  
관련 Apache HTTPD 파라미터인 maintain 값에 의해 60초마다 worker process가 가진 정보가 초기화 되고 있었습니다.  

그러면 worker process는 다시 main process로부터 IP정보를 받아와야 됩니다.  
이 시점에서 main process가 정상적인 IP를 제공하면 되는데 엉뚱하게도 과거의 IP를 제공합니다.  
기동시에 파악했던 최초의 IP들만 넘겨주는 것인지 아니면 그것과 함께 추가된 IP들을 제공하는 것인지는 모르겠으나  
어쨋든 worker process가 또 다시 잘못된 IP에 연결을 시도하고 14초의 응답지연이 발생합니다.  

### Apache HTTPD의 버그?

과거의 환경에서는 ELB처럼 DNS 정보가 수시로 바뀌는 상황은 없었습니다.  
DNS 정보 추가가 될지언정 바뀌지는 않는다고 하면 main process 에서 보관하는 것이 성능측면에서 유리하고  
WAS recovery 상황을 위해 연결실패한 IP도 버리지 않는 것은 올바른 방법이라고 생각됩니다.  

만약 DNS 정보가 바뀌는 상황이 있다해도 작업시기에 재기동을 같이해주면 해결되는 상황이었습니다.  

AWS같은 새로운 환경에서 사용이 어려운 것일 뿐이지  
기존의 환경에서라면 문제없이 최적화된 성능을 제공해주는 솔루션입니다.  

추후 버전에서는 Dynamic DNS Resolution 기능도 제공할 것이라고 생각됩니다.  
버그가 아닌 사용자의 적절치 못한 솔루션 선택이었습니다.  

## 해결 방안

### HAProxy 으로 교체

HAProxy의 최신버전에서는 Dynamic DNS Resolution 기능이 제공되고 있었습니다.  

이것을 테스트 해볼 시점에서는 DNS 기반 HA구성에 대한 Apache HTTPD의 기능약점이 원인이라는 것을 알았기 때문에  
굳이 ELB의 scale in/out을 통한 테스트가 아닌 Router53의 IP정보를 수동으로 바꾸는 방법으롵 테스트를 진행했고  
AWS 직원과 커뮤니케이션 없이 자체적으로 쉽게 테스트 할 수 있었습니다.  

테스트 결과 DNS server에 등록된 목적지 IP 정보가 바뀌면,  
Dynamic DNS Resolution 설정을 하지 않으면 Apache HTTPD와 비슷한 결과를 가졌지만  
Dynamic DNS Resolution 설정을 하면 거의 즉시 올바른 IP로 연결되고 정상상태가 계속 유지되는 것을 확인했습니다.  

나름 적절한 해결방안이었지만  
보안팀에서 HAProxy를 Web server로 인정해주지 않아서 Web-WAS-DB라는 3 tiers 구성이라는 보안팀 요구사항을 충족시킬 수가 없었습니다.  

### Nginx 으로 교체

유료 솔루션인 Nginx Plus의 경우는 Dynamic DNS Resolution 기능이 제공되고 있었지만  
무료 솔루션인 Nginx Open의 경우는 몇가지 기능이 제공되지 않는데 그 중 하나가 Dynamic DNS Resolution 입니다.  

하지만 Nginx Open으로도 도메인 변수처리와 rewrite 설정을 교묘하게 잘 엮으면 Dynamic DNS Resolution 기능을 동일하게 만들어낼 수 있었습니다.  

이 또한 설정해서 HAProxy와 같은 방식으로 테스트해봤고 문제가 해결되는 것을 확인했습니다.  

다만 Nginx Open이 제공하는 정상적인 설정방법이 아니고  
상용버전의 차별점이다보니 추후에 버전이 올라가면 사용할 수 없을 수도 있다는 리스크가 예상됐습니다.  

### NLB 사용

NLB는 다른 ELB와 달리 AZ별로 고정 IP를 할당 받습니다. AZ가 두개라면 고정 IP를 두개 받게 되고 추후 IP변경은 이뤄지지 않습니다. NLB의 scale in/out이 일어나도 IP변경은 일어나지 않기 때문에 문제가 발생하지 않습니다. 또한 고정 IP를 Apache HTTPD 설정에 직접 입력 가능합니다.  

![NLB사용](https://lh3.googleusercontent.com/kljFyu4IE8Mw_nWbUYsEA3ASEzdNCZN8hl7r7zJK7_CsOMgwsNPPKpAPBxv_rHMaLVLm8ECNQ4kSD9dQtWIWtRTeAMRW5K0-6n7N1N5hSg9ADfEGE3pX97f06uLYNcS85_RMUyi54TgccsKCQpv9oTs47rPwfzy-zIYS8C9k7K-JDHFkKOWwXX9wMoWy2g2n4ZrEe_JZXv1p6EzW8U8hlII7LGm2pqv_ltINzcBJfaJQDPhXrvyozRtaLtumlnBdCyjEuZsWEVdyLImcKNQX1DPU3voUniTxMeqQLhtrNY0S8EdfLnhBlxMJjzYNbaRtG1h7HWJxYg0zFJqCspKW23mPlCeGnMdMoJzgnLu2dZ0xFjAz93DgoNitHTMNqJfJsvozOQo4XrUicrg-BVC8bnM20r8fqeFWR8t0K362IuoTHqiPrMJDtFFrIAhxefSquBDPgaquRHYsvc6P2-fK4e3J5yGR3qJyqPZcTO6NU1AxTJenEI1lJQ225zvYOQEqYNp6M6MV-wHjCj5zf84oT987yTv1B5OSME6KoX-Jpx-Wii1eHXwKKVtGszRRAwRCvBNVksWi8k10BpnrMiDlOnhATjv57W42TNpCqdGfPWVEsYiU6RIRtB4C4__nLmBG5qchs8jB6ulojiFxKytajKYOoP3euPwT=w608-h514-no)

NLB는 같은 AZ에 위치한 WAS에게만 로드밸런싱 할 수 있는 Corss-AZ 처리불가 등 다른 ELB와 다른 특성이 몇가지 있는데 이 글과는 무관한 내용이니 생략하겠습니다.  

### Web-WAS 직접 연동

WEB과 WAS 사이의 ELB를 제거하고 WEB에서 WAS로 직접 연결 시킵니다.  
WAS의 IP 혹은 도메인을 Apache HTTPD에 직접 설정하다보니 WAS auto-scaling 설정이 불가능해집니다.  

연동은 AJP, Proxy-Pass 방식 모두 가능합니다.  

![Web-WAS 직접 연동](https://lh3.googleusercontent.com/l9TSZtOQgVn-2d-8mkLR9jw-rPxFE0T4ppF5GCyCuKfz1BXDVq0ZvIEFnB-pQw6_84ocO025bse1UMwn7gBc5BlJ6kI7Jib_dAxBDi1zSpJOrBveXF34ynB8F9xOQvOLaJ47WYnFSPt2TLR2NG61PvZi4ayTLsgVYNyYuKu6S1eYC6YgnZcu0VTydD6-1Z2zKsDrTzZ4nMWswMy_bRms5XUM7sK5ht9QSaglMwJyq6ckfKXCIWd_E3fKpjTrjn9M5p9ZT9xJH5uUA_-qSkxd8oZO-e1hmMkiBnjeNGQd7cXxW92-ZyS5PfgPkIw52JNbUht4jnLusY6ewwosu21S-UdK15j1j39py1Elsn3lpEvq4p6N5CaYb9ZXtLxHn-NON3SIfv2373G-twOwXPi1Uzd-mNfm8Eft-K94xTnOEIjWkLsj5I2ozZ-NFVXhz31ijKT2hhN3ls-A7xGVQxJ0ZcElUC2bNQxUlDlSU9t-6rPiRlqY-MdYtG7jXQ9orNJ-0f61DJbUX9doFAAYk0bXyCNiolaYHZYtKQ18rQ0YR_uGlmgNoDp5i0InFVxLKOJv4lEit-w0e_5VVKkRjycWVawKkwfCiFeb2oD5mDF_W_QD10miNWV6vIgGuWq1z4SzURG30PB9A6OYmNihuvX1nh8Gj7mI4vxg=w592-h506-no)


### Web 서버 제거

ELB의 IP변경에 대한 Apache HTTPD의 기능제한이 있었던 것이기 때문에 Apache HTTPD를 제거하고 구성하면 해결됩니다.  
SSL처리는 ELB(ALB, NLB)에서 가능합니다. 포트 포워딩과 URI rewrite 기능도 ELB에서 가능합니다.  

그 외 다른 Web서버의 기능은 WAS에서 처리하거나 다른 방식의 고민이 필요합니다.  
또한, 이미지, CSS, JS 등과 같은 정적리소스에 대한 처리를 어떻게 할지 고민이 필요합니다.  

![WEB제거](https://lh3.googleusercontent.com/23AkEkLOj5O_mK1lnwGKnBOWyc4vgCNYNw_eO-Canqi3Q8vGohppJjGBeCrPyrI5gLpHNN-7h4WrCz-1sgn42zikwPCczEMKvU_JNAc8HlW76hoJQaSR5fd-MLfsHt9il8ThrNe_80y9dQ5ua2XnpjCQr8RHCm8swszo3K15p3hieqkHPgHltFkiejgIIcfy-UJX5U-hMxl7-SpuLFXL_Ss6gb6r0ZLyYDNshmsxPmqMDmc6yWyqYQLSRQIXV8EmcNICZFZWVzpZ6V2hYvmKztCK05mw1lYQeG3Ays7Bm90vqGys9matn12o_gQjo8NNHQqhQkoxPXJYU-knuzCVW1J4ZU26xcS21yk1-zHyfF21iw2zgxqHRIEg0kHjETD0wxcb8uCVUJNTU5-W4yFQaMKoNe3YWdN1JyZNA4SZuIJdb3jW674mHfJtIsu4gZduO_ezbbfQjkqJCkEZgMcPI5oTLKzdUXfu4X_NjFGkUJooW4QZtMjFkoYL4I4T2eRd0S1w0oQ-RFxc3EB4Fl0leiFS1tDFX-F95cYHBhcZlwGbx1AVtsn4Nh8YRuSaFq-aEgwfWGd8CnK1EyQSiQsIuxDj8VJ-149Xyp2-oy49FPJJieYORIyerbgqvXy4e0L4prvn2U8pdILEOHazjSfquh8Zd7PQqtKp=w602-h522-no)


## 진행 결과

원인분석과 해결방법을 찾는데는 시간이 걸릴 것으로 예상이되어 일단 Web-WAS 직접 연동으로 처리했습니다.  
Auto-scaling은 포기해야했지만 자원 사용량에 이슈가 없었기 때문에 그 상황에서 가장 확실하다고 생각한 기존에 해오던 방식인 Web-WAS를 AJP로 직접 연동처리 했습니다.  

그 후 다양한 가설과 테스트 과정을 거쳐서 원인이 분석되었고  
HAProxy, Nginx로는 처리가 가능한 점과 Apache HTTPD를 유지하려면 NLB를 사용해도 되고 사실 Web 서버 자체를 제거해도 된다는 점을 가지고 논의를 했습니다.  

당시에는 제가 구축, 운영을 하고 있었지만  
안정화된 이후에는 타 운영팀으로 업무이관을 하는 상황이었기 때문에 운영팀의 skill-set, 프로세스를 고려해야했으며  
개발, 검수, 운영 환경 모두 포함해서 제가 혼자 관리하던 서버 대수가 200대가 약간 넘을정도로 작지 않은 프로젝트였기 때문에 결정이 빠르지 못했습니다.  
특히, 보안팀과의 보안정책 협의에 시간이 많이 걸렸습니다.  

사실 Dynamic DNS Resolution 관련 이슈가 발생하기 전부터 
프로젝트에 처음 투입되었을 때부터 Web서버가 왜 필요한지 의문이 들어서 언급한 적이 몇번 있었습니다.  
제공하는 서비스가 REST API 서버의 역할이 대부분이어서 이미지, CSS, JS 등과 같은 정적리소스를 처리하는 내용이 거의 없었고 Web서버에서 처리하는 내용이라고는 SSL, 포트 포워딩 정도밖에 없었기 때문에 ALB에서 모두 처리 가능하여 Web 서버를 제거해도 문제가 없어보였습니다. 게다가 관리포인트도 줄어드는 큰 장점도 있습니다.  

하지만 당시의 보안 정책상 Web-WAS-DB라는 3 tiers구성을 형식적으로라도 지켜야하는 상황으로 전개되어 진행하지 못했고 Dynamic DNS Reslution 이슈가 발생한 뒤에도 또 몇차례 언급을 했지만 진행되지 못했습니다. 사실 관련해서 보안팀과의 협의가 이뤄졌는지 아니면 중간에서 무시됐는지 조차 확실치 않습니다.  

그 과정에서 다른 방법을 찾기 위해서 HAProxy, Nginx로 대체하는 방법도 제안했던 것입니다.  

최종적으로는 엉뚱하게도 비용관련 이슈를 통해 문제가 해결됐습니다.  
AWS 비용을 줄일 방법을 고민해달라는 요청을 받아서 Web서버를 제거하면 불필요한 비용도 기술이슈도 해결되며 관리포인트도 줄어든다는 방향으로 설득을 했더니  
결정권자들이 적극적으로 움직여서 최종적으로는 보안팀과의 협의를 이뤘고 Web서버를 제거하는 방향으로 진행됐습니다.  

## 추가내용 

### 차후 ELB 변경사항

2018.03 말에 AWS 컨설턴트로부터 곧 ELB에 대한 업데이트가 있을 것이라는 얘기를 들었습니다.  
ALB는 NLB 처럼 고정 IP를 할당 받게 되고 NLB는 Cross-AZ 처리가 가능해진다는 내용이었습니다.  

현재(2018.07) 업데이트가 이뤄졌는지까지는 모르겠습니다.  
다만 또 다시 AWS의 테스트베드가 될 것으로 예상되어  
(NLB 최초 출시 때도 잠깐 사용했었는데 다른 이슈들이 발생해서 다시 Web-WAS 직접 연동으로 처리했습니다.)  
AWS 기능 업데이트에 의존하지 않는 방향으로 의견이 모아졌습니다.  

하지만 AWS에서 제공하기로한 기능이 정상적으로 처리 된다면  
Apache HTTPD, Nginx 기본설정만으로도 문제 없이 ALB와 연동이 가능해 질 것으로 보여집니다.  

### 왜 14초인가?

추후에 작성 예정

# Aurora DB Fail-Over

운영 중에 발생한 건은 아니고 DB의 HA 테스트 과정에서 있었던 이슈입니다.  
Aurora RDS Fail-over 테스트를 진행했고 생각보다 서비스가 재개되는데 상당히 긴 시간이 걸렸습니다.  
WAS의 JVM DNS TTL과 OS의 DNS TTL을 0으로 설정하고 테스트를 하니 3초 정도까지 시간이 크게 줄어들었습니다.  
하지만 Oracle RAC를 기준 잡혀있는 품질팀으로부터 요구사항은 1초 이내였습니다.  

## Aurora RDS

Aurora RDS에 대해서 간략하게 설명하겠습니다.  

Aurora RDS도 DNS 기반으로 HA가 확보된 AWS 서비스입니다.  
이중화 구성이 되어 있는데 하나는 읽기, 쓰기가 가능한 writer 역할이고  
다른 하나는 읽기만 가능한 reader 역할을 가진 DB입니다.  

이 역할은 고정된 것이 아니라 기동시 정해지고 writer에게 문제가 생기면 다른 DB에게 writer 역할이 넘겨집니다.  

각 DB server는 각자의 도메인을 갖고 있지만 (db01.aws.com , db02.aws.com)  
상황에 따라 역할이 바뀌기 때문에 WAS에서는 해당 도메인을 사용하지 않습니다.  
Writer 역할을 갖는 DB를 가리키는 도메인이 하나 더 제공되며 (db-writer.aws.com)  
WAS에서는 그 도메인을 가지고 연결을 시도합니다.  

![Aurora DB Fail-Over](https://lh3.googleusercontent.com/p_BM_qDa7YkLnKMgRn4R0RDTaS4tk6h3ChfqitjubrGoe1bnnU2_h9PoVphfBs_pBwkSluApWviNOOsHEZD0PYapp3IIVyLw3tVkj18M2EejlN2RFB8_E7xdzfcl0D9RMg68hGBdPgr4I_mVJRD7Ar7jeDizP1LIXB-L-qeL-bgdOphcW425YIiZUhGztZui4sXoL9NGN8GJ-_RbkDUqCE8Qs6IeHLXkEEHk5kwmIB9p4JYtTZBO1x0FBvjb9p8CF88W6vD9mtmT8FzZrXXCyueMMiJbhEOEyhwk9T8LuW9E0daPtHtSW5nd3oM5X9wjKjdYQR3RFiRzm1Qf-mxjx9hK7lg-y4Z6rKon9qze-ZQ8jBAwuClN4o7xNp77-7qvqZJPQkrQ28mzM5n_FyERXsep9aSe4-OwfiC3a2GWLrBCNSttpHJUtgN0D3KcQIglXhb-7cyp8rlgx1MJoCnGCt2hq7fTHiR5gCg4sWUNPZbmyUM6pnOOulLh4skpI6iNvvhvuPSk6LG-_8vlZTEp_LcKuuic3zFhsH7K08fXYQzyENs8Uqi0uhW2zijZBFrgPCTMhYPWPzjIK8TlISuyYVTB8wCy-pEBJTk5Da92SrlhXI8mMqhga31tv-my0B-EZvRR_wuBpR3D2gCK80DrSD04T3-oZZRi=w520-h510-no)

## 테스트 

어느 구간에서 시간을 잡아먹는지 파악하기 위해 테스트를 진행했습니다.  

  1. 이슈인지 : Writer DB (DB01) 이슈발생 인지
  2. DB재기동 : Writer DB (DB01), Reader DB (Db02) 재기동
  3. 역할변경 : Writer DB (DB01->DB02), Reader DB (DB02->DB01)
  4. DNS반영 : DNS에 정보 반영
  5. WAS인지 : WAS에서 DNS 정보 인식 
  6. 정상연결 : WAS에서 Writer DB (DB02)로 정상연결

WAS에 적당한 부하를 줘서 계속해서 DB에 query를 보내는 상황에서  
강제적으로 Writer DB에 이슈를 발생시켰습니다.  

WAS에서 Exception 발생 시작시점과 추후 정상처리 시작시점을 기록했습니다.  
그리고 간단한 스크립트를 짜서 nslookup을 계속 질의하여 Router53에 IP가 반영되는 시점을 확인했습니다.  
DB의 로그를 전달받아서 이슈가 인지되고 재기동, 역할변동들에 걸리는 시간을 체크해봤습니다.  

## 원인분석

상황이 인지되어 DB가 재기동되고 역할변경이 이뤄지는데까지는 짧은 시간이 걸렸습니다.  
그리고 WAS에서 IP가 변경됨을 인지하고 DB에 정상적으로 연결되는데 까지 걸리는 시간도 굉장히 짧았습니다.  
결국 DB와 WAS는 각각 빠른 일처리를 해지만 DNS에 DB IP 변경사항이 반영되는데 걸리는데 대략 3초 정도의 시간이 걸리는 것으로 보여졌습니다.  

## 진행 결과 

AWS 측에 문의해봤으나 당시로써는 해당구간에 대해서 더 이상의 시간단축 방법은 없었습니다.  
꽤나 고생을 했지만 결국은 제어할 수 없는 영역에서의 처리시간이었고 품질팀과 재논의를 통해 적당히 타협해서 이슈를 종료시킬 수 있었습니다.  

