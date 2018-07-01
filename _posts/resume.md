---
layout: plain
title:  "resume"
date:   2018-07-01 00:00:00
lastmod: 2018-07-01 00:00:00 
categories: Resume
tags: Side-Projects
---

현재 회사에서는 Software-Architect 로 불리는 역할을 수행 중입니다.  
이 회사에서 Software-Archtect 의 역할은  
원래는 OS 위에 올라가는 Middleware, Software에 대해서 설계, 구성하고  
Framework 설정하고 업무로직이 포함되지 않은 AOP, 공통모듈, 연계모듈 등을 프로그래밍하고  
CI/CD 구성, 성능테스트, 트러블슈팅 등을 수행합니다.  

근데 실제로는  
TA 없다고 라우팅, 방화벽 오픈 요청/테스트 하고  
서버 생성 요청해서 OS 설치된 서버 받으면 계정만들고 디렉토리 정해서 권한 주고  
업무로직 개발도 하고 이것저것 얕게 다 하고 있습니다.  
어떻게 보면 다른 개발자들을 위한 잡부가 되어버린건 아닌가라는 생각이 듭니다.  

<br>

Side-Projects에서는 AWS Lambda를 주로 활용하여  
잡부가 할일을 AWS에게 시키고 프로그래밍에 최대한 전념하고 있습니다.  

사실 미들웨어 구성하고 설정하는 이런 업무보다는 프로그래밍하는 것을 좋아합니다.  
업무 개발자로 시작해서 프로그래밍을 재미있게 하고 있었는데  
입사 1년차 말에 Side-Projects를 시작해보니 미들웨어, 프레임워크에서 크게 부딪혔습니다.  
그렇게 시작해서 계속 부딪혀오다보니 지금은 Google Search만 있으면 뭐든 해낼 수 있을 것만 같은 잡부가 되어버린거지  
원래는 프로그래밍하는 것을 더 좋아합니다.  

<br>

제가 추구하는 저의 커리어는 Back-End 개발자입니다.  
프로그래밍만 해도 된다면 Back-End 프로그래머라고 했을텐데  
현실상 불가능하니 Back-End의 구축, 운영을 위해 이것저것 다 하는 developer가 되기로 마음먹었습니다.  

프로그래밍 언어는 크게 신경쓰지 않습니다.  
Java가 주력 언어이고 최근 side-projects는 대부분 Python3로 진행하고 있습니다.  
회사에서 C#으로 프로젝트 진행 경험도 있습니다.  

Javascript, JQuery 혹은 Android도 못하는 편은 아니지만  
Front-End 영역은 제게 호감이 가는 영역은 아닙니다.  

# Side Projects

개인적으로 진행한 프로젝트들을 기록합니다.  

일단, 나름 출시(?)를 하고 사용자를 만들어본 프로젝트에 대해서만 기록합니다.  

열심히 만들기는 했지만 보여줄 방법이 없는 몇몇 예전 사이드 프로젝트들이 생각나서 마음이 아프네요 ㅎ  

[Dveamer`s Android Apps](https://play.google.com/store/search?q=dveamer&c=apps)에서 현재 운영 중인 제가 만든 안드로이드 앱들을 보실 수 있습니다.  

## 고양이 방울 ( Belling The Cat )

![Belling The Chat](https://lh3.googleusercontent.com/stIk_WOZuB1ptZEOi23f0QOxKNPp25Z_Gp4J0XY5dVIl764cCIBJQKbxnel01qqlMls=s180){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.goso](https://play.google.com/store/apps/details?id=com.dveamer.goso)
  * 2018.04 ~ 
  * 다운로드 수 : 1,000 이상 (2018.06 기준)

동일한 주제를 등록한 사람들끼리 글과 이미지를 공유하는 앱입니다.  
그 주제에 대해서 정말 알고 있는 사람만 접근 가능할 수 있도록 만들었습니다.  
앱을 만든 목적과 사상을 설명하려면 좀 길기 때문에 [Google Play](https://play.google.com/store/apps/details?id=com.dveamer.goso)에 올려둔 설명을 읽어봐주시기 바랍니다.  

이 앱을 통해서 Android, AWS S3, AWS Lambda를 가지고 이미지를 다뤄봤습니다.  
이미지 업로딩은 시간이 오래 걸리기 때문에 AWS Lambda를 거치도록 설계하면 비용문제가 생길 수있습니다.  
Android가 AWS S3로 이미지를 직접 올리려면 인증, 권한 문제를 해결해야하는데  
AWS S3에서 제공하는 pre-signed URL을 이용해서 진행했습니다.  
기존의 client -> server -> repositories 형태를 벗어나  
client -> repositories 구조이면서 인증, 권한을 해결하니  
serverless 아키텍처를 이제서야 제대로 활용했다는 생각이 들더군요.  

이미지 리사이징(re-sizing) 같은 처리도 했는데  
기존에 이미지를 다뤄본 경험이 많지 않아서 흥미로웠습니다.  
AWS를 사용하면서부터는 자주 사용해오던 Event-Driven 패턴으로 설계했는데  
이미지 리사이징에서는 다른 때보다 사용자 체감 측면에서 큰 효과를 얻을 수 있었습니다.  


### 제공하는 기능

  * Topic List : 상점, 사람과 같은 주제를 등록합니다
  * Messages About Topics : 주제에 대한 글을 쓰고 다른 사람들의 글을 읽을 수 있습니다
  * Alarm : 등록해둔 주제에 다른 사람들이 글을 쓰면 알람을 줍니다

### 사용된 기술

  * Front-End : Android Java
  * Back-End : Python, AWS Lambda, AWS S3, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS Front

## BG Log

![BG Log](https://lh3.googleusercontent.com/0ixqBsJvTIbdo22rKEsV7LkXddmZHtlcHK1DgGD9yxpvR43wfB41GSnLqq-lf_Xhlu4O=h310){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.pubglog](https://play.google.com/store/apps/details?id=com.dveamer.pubglog)
  * 2017.09 ~
  * 다운로드 수 : 10,000 이상 (2017.11 기준)

배틀그라운드(PUBG / Battle Grounds) 게임 점수 갱신시 알림을 주는 앱입니다.  

저도 그렇고 회사 동료들도 에이밍이 좋지 않아서 해당 게임을 하지 않지만  

딱 한명이 한다길래.. 그 한명을 감시하고 놀려먹기 위한 만들었다는........  

지인의 게임 점수가 변동되면 알림을 주고 그 내용을 그래프로 기록해줍니다.  

### 제공하는 기능

  * Score Board : 지인들의 게임점수를 한눈에 보여줍니다
  * Score History : 지인의 게임점수 히스토리를 그래프로 보여줍니다
  * Alarm : 지인의 게임점수가 변동되면 알람을 줍니다
  * Search History : 배틀그라운드 게임계정으로 지인을 검색합니다
  * Chat : 등록한 계정별로 대화방이 존재하며 해당 계정을 지인으로 등록한 사람들과 대화를 나눌 수 있습니다

### 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS Front, Google Firebase

## OW Friends

![OwFriends](https://lh3.googleusercontent.com/DB7QF5c5b6n5uLWpCzGo8ZtWOavIVotAvvhgmLeGOL8wTv4AwU16s41QiNTrDlKtLMM=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.owfriends](https://play.google.com/store/apps/details?id=com.dveamer.owfriends)
  * 2017.02 ~
  * 다운로드 수 : 1,000 이상 (2017.11 기준)

오버워치(Over Watch) 게임 점수 갱신시 알림을 주는 앱입니다.  

원래는 회사 동료들이 게임에 접속하면 같이 접속하기 위해 만들기 시작했는데....  

원천적으로 불가능한 문제가 있어서 목적이 변경 된 앱이 탄생했습니다.  

지인의 게임 점수가 변동되면 알림을 주고 그 내용을 그래프로 기록해줍니다.  

회사 동료가 늦잠자서 반차썼을 때 게임했는지 하지 않았는지 감시하고 놀려먹기 위한 앱으로 변질됐다는.....  

나중에 회사 동료의 부주의로 회사 상사에게 앱이 노출되어 회사에서 피곤하다고 말도 못 꺼냈다는.......  


### 제공하는 기능 

  * Score Board : 지인들의 게임점수를 한눈에 보여줍니다
  * Score History : 지인의 게임점수 히스토리를 그래프로 보여줍니다
  * Alarm : 지인의 게임점수가 변동되면 알람을 줍니다
  * Search History : 배틀그라운드 게임계정으로 지인을 검색합니다
  * Chat : 등록한 계정별로 대화방이 존재하며 해당 계정을 지인으로 등록한 사람들과 대화를 나눌 수 있습니다

### 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS Front, Google Firebase

## Perfect Trainer

![Perfect Trainer](https://lh3.googleusercontent.com/S9jtmDIv013LFY971gFA36mOYoKleo5iu-GT6lT2BbDzSbCvsriG3L7D1IQ5ZZ5SJw=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer)
  * 게시기간 : 2017.02 ~ 2018.05
  * 다운로드 수 : 1,000 이상 (2017.11 기준)

등록해둔 포켓몬고(Pokemon Go) 몬스터가 주변에 나타나면 알람을 주는 앱입니다.  

망나뇽이 나타났다는 소식에 일하다 말고 회사동료들과 사무실 주변을 열심히 뛴적이 있었습니다.  

'망나뇽이 뭐라고 우리가 이렇게 뛰고 있지?'  

라는 의문이 들었지만, 그 순간 느꼈던 짜릿함은 잊을 수 없었습니다.  

회사에서 놀려고 만든건 아니고.. 그 짜릿함을 더 느껴보기 위해 만들었다는 핑계.......  

의외로 자녀분들을 위해 희귀 몬스터를 찾아 떠나시는 과장님들이 선호하더군요.  

### 제공하는 기능

  * Wanted List : 알람받고 싶은 몬스터들을 등록합니다
  * Found List : 설정해둔 거리 이내에서 나타난 몬스터들을 보여줍니다
  * Alarm : 설정해둔 거리 이내에서 몬스터들이 나타나면 알람을 줍니다
  * Showing Map : 찾은 몬스터의 위치를 지도에서 보여줍니다

### 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS Front

## Blind Date

![BlindDate](https://lh3.googleusercontent.com/DmgMzZ7DPwpxbFaxw06ydruPNyghpt8vJ_yKCo8ics6KQF9xeaZ6RG-awDKUHMqeiz0=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.blinddate](https://play.google.com/store/apps/details?id=com.dveamer.blinddate)
  * 2015.08 ~ 2017.10
  * 다운로드 수 : 50 이상 (2017.11 기준)
  * 현재 사용자가 없음에도 EC2 비용이 발생해서 Google Play에서 게시중지시켜 둠 (2017.10 기준)

사용자가 주선자가 되어 지인과 지인을 연결시켜주는 소개팅 앱 입니다.  

실제 오프라인 소개팅을 온라인으로 옮겨두었다고 보면 되지만 소개받은 지인들은 주선자가 누군지 알 수 없다는 것이 앱의 특징입니다.  

친구에게 소개팅을 시켜줬다가 결과가 좋지 않았던 것을 잊지 못하는 소심한 개발자는  

자신의 존재를 노출하지 않고 서로 잘 어울릴 것 같은 두 사람에게 대화의 기회를 만들어 주기 위해 앱을 만들었지만....  

아무도 앱을 깔지 않아서 소개팅을 시켜주지 못했다는 슬픈사연이...  


### 사용된 기술

  * 환경 : AWS VPC, EC2, Ubuntu
  * Front-End : Android Java
  * Back-End : Java, Socket, HTTP, Spring Boot Framework, Netty Framework, MariaDB, AWS APIGateway, AWS Route53
  * 구성 : Manager Server, Chatting Server, Push Server

