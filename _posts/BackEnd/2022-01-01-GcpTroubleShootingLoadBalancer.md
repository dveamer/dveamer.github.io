---
layout: post
title: "LoadBalancer 관련 GCP 트러블슈팅"
date: 2022-01-01 00:00:00
lastmod: 2022-01-01 00:00:00
categories: BackEnd GCP Troubleshooting 
tags: BackEnd GCP Troubleshooting LB
hidden: false
published: true
---

![GCP Load-Balancing](https://cloud.google.com/load-balancing/images/regional-ext-https-lb.svg){:class="imgTitle"}  
(이미지 출처 : [https://cloud.google.com](https://cloud.google.com))  

GCP를 이용해서 프로젝트를 진행하면서 겪은 트러블슈팅 중 load-balancer 와 과련된 건들을 공유합니다. 

<!--more-->

## KeepAlive 설정 필요

GCP HTTP(S) LB는 keep-alive 값을 10분으로 고정합니다. GCP HTTP(S) LB 뒤에 연결된 모든 백엔드 서버는 keep-alive 설정을 반드시 10분 이상으로 잡아줘야 됩니다.  

> HTTP 연결 유지 제한 시간: 값이 10분(600초)으로 고정됩니다. 백엔드 서비스를 수정하는 방법으로 이 값을 구성할 수 없습니다. 백엔드가 사용하는 웹 서버 소프트웨어는 백엔드가 연결을 조기에 닫지 않도록 연결 유지 제한시간을 600초보다 길게 설정해야 합니다. 이 제한 시간은 WebSocket에는 적용되지 않습니다. 다음 표는 일반적인 웹 서버 소프트웨어의 연결 유지 제한 시간을 수정할 때 필요한 변경사항을 보여줍니다.  
> 출처 : [외부 HTTP(S) LB](https://cloud.google.com/load-balancing/docs/https), [내부 HTTP(S) LB](https://cloud.google.com/load-balancing/docs/l7-internal)  

만약 10분보다 짧게 설정되어있다면 LB에서는 연결이 유지되어있다고 가정하고 동작하지만 실제로 서버에서는 연결을 정리해둔 상태라 에러가 발생합니다.  
서버에서 설정한 keep-alive 값이 1분이라고 하면 1분 동안 트래픽이 없다가 1~10분 사이에 발생하는 소켓별 첫 트래픽들은 에러가 발생할 것입니다.  
특수한 상황에서 발생하는 케이스라 발견이 쉽진 않습니다.  

LB 앞의 client와 LB의 로그에서 확인이 가능합니다. 근데 로그를 발견한다 하더라도 원인을 keep-alive로 연결시키기는 쉽지 않았습니다.  
다른 CSP의 LB는 콘솔화면에서 keep-alive를 설정하는 항목이 있어서 의심을 시작해보기가 쉬운데 GCP는 기본값으로 문서에만 나와있고 콘솔화면에 없다보니 놓치기 쉽습니다.  


### 발생했던 상황

두가지 케이스가 있었습니다.  

  1. browser -> LB -> server
  2. client -> LB -> server

1번의 상황은 정말 한두번 "메인화면에 연결이 안되고 에러화면이 떴어요."라고 누군가가 제보를 줬는데 LB 로그를 확인해보면 잠깐 에러가 나있고 서버 로그에는 남아있는 것이 없으며 재현이 불가하여 원인을 찾을 수가 없었습니다.  
해당 에러 메시지는 안타깝게도 현재 제가 기록해둔 것이 없습니다.  

2번의 상황은 좀 더 빈번하게 발생했습니다. 그리고 client의 설정을 변경할 수 있는 상황이었습니다. 물론 당시에는 1번과 2번이 동일한 원인이라고 생각하지도 못했습니다.  
server는 특정 업무를 위한 솔루션 프로그램이 설치되어 특정 상황에서만 트래픽이 들어가는 케이스라 좀 더 에러 발생이 자주 발생했던 것 같습니다.  
하루에 1~2회 발생하는 날도 있었고 운 좋으면 한시간에 1번씩 발생하는 날도 있었습니다.  

여기서부터는 저희가 진행했던 삽질을 간략하게 적어보겠습니다.  

Client는 Netty 위에 돌아가는 Webflux로 아래와 같은 에러메시지가 남아있었습니다.  

~~~
io.netty.channel.unix.Errors$NativeIoException: readAddress(..) failed: 상대편이 연결을 끊음
~~~

먼저 client의 버그를 의심했습니다. Client에서 사용하는 라이브러리들의 버전을 최신 버전으로 올리기도 했었고 검색을 하다보니 동일한 에러 메시지가 났었는데 epoll 방식에서 NIO 로 변경했더니 에러가 없어졌다는 글을 발견해서 epoll 라이브러리를 exclude하고 NIO로 교체했습니다. 하지만 첫날에는 에러가 발생하지 않아서 좋아했었는데 그것은 우연이었고 몇일 기다려보니 결과는 동일했습니다.  
두번째는 혹시나 하는 마음에 LB를 제거하고 client에서 server로 직접 연결을 시켜봤습니다. 그렇게 두고 이틀을 지켜봤지만 문제가 발생하지 않았습니다.  
LB가 문제인가? 라는 의심은 했지만 GCP 콘솔의 LB 설정 화면을 열심히 살펴보고 변경해보고 몇일 모니터링하는 것을 계속 반복했지만 해결되지는 않았습니다.  
긴 시간동안 들여다봤지만 계속해서 해결은 안되고 client에서는 fallback 기능이 구현되어있어서 큰 문제로 삼는 사람도 없고 다른 바쁜 일들은 계속 생겨서 해당 건은 점점 묻혀지는 상황이었습니다.  
그러다가 동일 client에서 GCP LB를 통해 연동하는 서버가 하나 더 추가됨에 따라 다시 이슈화가 되어 원인을 찾게 되었었습니다.  

<!--ads-->

## POST 에 대한 Retry

2021년도 GCP LB의 retry 기능에 버그가 있었고 그로인해 발생한 트러블슈팅 내용입니다.  

[외부 HTTP(S) LB](https://cloud.google.com/load-balancing/docs/https), [내부 HTTP(S) LB](https://cloud.google.com/load-balancing/docs/l7-internal)의 기본 retry 정책은 서로 다르고 수정 가능합니다. 하나 공통점은 POST에 대해서는 재시도를 하지 않는다는 점입니다. (GET, PUT, DELETE는 멱등성을 보장해야하는 HTTP method )  

근데 POST API를 호출 후 발생한 5xx 에러건에 대해 이중요청이 들어오는 문제가 발생했엇습니다.  

**Browser -> LB -> G/W -> server** 와 같은 구조에서 발견되어 처음에는 G/W 설정 문제를 확인했으나 LB가 없는 로컬환경에서는 재현이 안되는 것을 확인했고 테스트 환경의 G/W의 access log에 2회 호출이 들어오는 것을 발견할 수 있었습니다.  
GCP 측에다가 문의하였고 처음에는 LB 문제가 아니라는 답변을 받았지만 테스트 내용을 가지고 재질의하여 LB 버그로 확인되고 조치하는 방향으로 진행됐습니다.  
재미있던 점은 서울 리전에서 이슈를 올린 것인데 조치는 미국쪽 리전들에 먼저 적용되고 서울 리전은 몇 주뒤에 적용되었다는 점입니다.  
해당 내용은 2021년도 상반기에 조치 된 사항입니다.  
  

