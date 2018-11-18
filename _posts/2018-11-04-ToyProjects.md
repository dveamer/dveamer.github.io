---
layout: plain
title:  "Toy-Projects"
date:   2018-11-04 00:00:00
lastmod: 2018-11-04 00:00:00 
---

개인적으로 Toy-Projects를 하면서 쌓아온 경험들을 기록하는 페이지 입니다.  
  
<!--more-->

나름 출시(?)를 하고 사용자를 만들어본 프로젝트에 대해서만 기록합니다.
열심히 만들기는 했지만 보여줄 방법이 없는 몇몇 예전 사이드 프로젝트들이 생각나서 마음이 아프네요 ㅎ  

토이 프로젝트는 회사에서 주로 사용하는 skill set과 무관하게  
학습하고 사용해보고 싶은 기술로 마음껏 진행해 볼 수 있어서 큰 도움이 됩니다.  
기술 외에도 프로젝트를 시작하고 출시하기 위한 전반적인 과정, 예상치 못한 이슈들을 경험하고 이해하는데도 도움이 됐습니다.  

현재 운영 중인 어플리케이션은 아래 링크에서 확인하실 수 있습니다. 

  * [Dveamer`s Android App](https://play.google.com/store/apps/developer?id=dveamer)
  * [Dveamer`s Chrome Add-On](https://chrome.google.com/webstore/search/dveamer)
  * [Dveamer`s Firefox Add-On](https://addons.mozilla.org/ko/firefox/user/dveamer/)

# Clean News 

![Clean News](https://lh3.googleusercontent.com/BaRgyqGNoX9TvOaDVXHIdCG5chXyrlk-AoDAVvC3d4mOJb2d0xb-JWv3RqffFX59BnILimO7Sw=w440-h280-e365){:class="imgTitle"}  

  * Chrome Add-On : [https://chrome.google.com/webstore/detail/clean-news/mkfndjjcdgdlgnpocgljgcembjpaoekj](https://chrome.google.com/webstore/detail/clean-news/mkfndjjcdgdlgnpocgljgcembjpaoekj)
  * Firefox Add-On : [https://addons.mozilla.org/ko/firefox/addon/clean-news/](https://addons.mozilla.org/ko/firefox/addon/clean-news/)
  * 2018.11 ~

뉴스를 깨끗하게 만들기 위한 프로젝트입니다.  

고의적으로 공해성 기사를 만들어내는 뉴스 사이트, 기자를 개개인이 차단할 수 있습니다.  
원치 않는 글을 만나서 소중한 개인시간을 낭비하는 일이 없었으면 합니다.  

자극적인 기사 제목, 악플만 달리는 내용의 기사들이 끊임없이 올라오는 이유는 돈이 되기 때문입니다.  
광고를 통해 돈을 벌기 때문에 욕을 먹더라도 노출수, 체류시간, 광고 클릭 수가 높으면 돈이 됩니다.  
게다가 악플들도 컨텐츠의 역할을 해서 조회수가 급격하게 높아지는 현상도 일어납니다.  

Clean News는 차단과 무관심을 통해 악성 기자들을 고립시켜보기 위해 만들어졌습니다.  

<br>

Front-end는 브라우저 확장앱(extension or add-on)으로 제게는 첫 시도 입니다.  
DOM이 아닌 브라우저에서 제공하는 API를 사용하는 과정에서 최근 deprecated 된 것들이 있어서 검색한 결과가 실제 돌아가지 않는 케이스가 있었습니다.  
그리고 Chrome과 Firefox 브라우저가 제공하는 API가 서로 다르고 제공하는 기능들이 달라서 포팅 과정이 쉽지만은 않았습니다.  

Back-End는 예전처럼 Python과 AWS를 이용한 것은 동일하지만 이번에는 Flask와 Zappa를 추가적으로 사용했습니다.  
기존에는 AWS Lambda에 Python으로 function 을 작성하고 AWS APIGateway와 function별로 매번 연동을 시켰었습니다.  
하지만 이번에는 Flask REST API 작성과 Zappa 배포를 통해 1회의 AWS APIGateway 설정만으로 시스템을 확장시켜나갈 수 있게 되었습니다.  

처음에는 Zappa 세팅에 시간을 뺐겼지만 나중에는 Zappa 덕분에 시간을 많이 단축할 수 있었고 로컬 테스트도 용이해졌으며 소스코드의 재활용성도 높아졌습니다.  

## 제공하는 기능

  * 뉴스 사이트 차단
  * 뉴스 기자 차단 ( E-mail, 기자 이름 )
  * 금칙어를 통한 차단

  * 차단 내역 관리 페이지

## 사용된 기술

  * Front-End : Javascript, Jquery, HTML, CSS
  * Back-End : Python, Flask, Zappa, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS CloudFront

# 고양이 방울 ( Belling The Cat )

![Belling The Chat](https://lh3.googleusercontent.com/stIk_WOZuB1ptZEOi23f0QOxKNPp25Z_Gp4J0XY5dVIl764cCIBJQKbxnel01qqlMls=s180){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.goso](https://play.google.com/store/apps/details?id=com.dveamer.goso)
  * 2018.04 ~ 
  * 다운로드 수 : 1,000 이상 (2018.06 기준)


일상 속 악당들을 고립시키기 위한 프로젝트 입니다.  
앱을 만든 목적과 사상을 설명하려면 좀 길기 때문에 [Google Play](https://play.google.com/store/apps/details?id=com.dveamer.goso&hl=ko)에 올려둔 설명을 읽어봐주시기 바랍니다.  

특정 사람이나 상점 등을 주제로 등록할 수 있습니다.  
그리고 동일한 주제를 등록한 사람들끼리 정보를 공유하는 앱 입니다.  

항상 남에게 피해를 주는 사람들이 있습니다.  
"그런 사람인줄 알았으면 처음부터 피했을텐데.." 라고 후회하지 않도록  
악당의 목에 방울을 달기 위한 프로젝트 입니다.  

<br>

이 앱을 통해서 Android, AWS S3, AWS Lambda를 가지고 이미지를 다뤄봤습니다.  
이미지 업로딩은 시간이 오래 걸리기 때문에 AWS Lambda를 거치도록 설계하면 비용문제가 생길 수있습니다.  
Android가 AWS S3로 이미지를 직접 올리려면 인증, 권한 문제를 해결해야하는데  
AWS S3에서 제공하는 pre-signed URL을 이용해서 진행했습니다.  
기존의 client -> server -> repositories 형태를 벗어나  
client -> repositories 구조이면서 인증, 권한을 해결하니  
이제서야 serverless 아키텍처를 제대로 활용했다는 생각이 들더군요.  

이미지 리사이징(re-sizing) 같은 처리도 했는데  
기존에 이미지를 다뤄본 경험이 많지 않아서 흥미로웠습니다.  
AWS를 사용하면서부터는 자주 사용해오던 Event-Driven 패턴으로 설계했는데  
이미지 리사이징에서는 다른 때보다 사용자 체감 측면에서 큰 효과를 얻을 수 있었습니다.  

## 제공하는 기능

  * Topic List : 상점, 사람과 같은 주제를 등록합니다
  * Messages About Topics : 주제에 대한 글을 쓰고 다른 사람들의 글을 읽을 수 있습니다
  * Alarm : 등록해둔 주제에 다른 사람들이 글을 쓰면 알람을 줍니다

## 사용된 기술

  * Front-End : Android Java
  * Back-End : Python, AWS Lambda, AWS S3, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS CloudFront

# BG Log (배그로그)

![BG Log](https://lh3.googleusercontent.com/0ixqBsJvTIbdo22rKEsV7LkXddmZHtlcHK1DgGD9yxpvR43wfB41GSnLqq-lf_Xhlu4O=h310){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.pubglog](https://play.google.com/store/apps/details?id=com.dveamer.pubglog)
  * 2017.09 
  * 다운로드 수 : 10,000 이상 (2017.11 기준)

배틀그라운드(PUBG / Battle Grounds) 게임 점수 갱신시 알림을 주는 앱입니다.  
배틀그라운드를 하지는 않지만 회사 동료 중 한명이 한다길래 그 동료를 위해(?) 만들었습니다.  
지인의 게임 점수가 변동되면 알림을 주고 그 내용을 그래프로 기록해줍니다.  

## 제공하는 기능

  * Score Board : 지인들의 게임점수를 한눈에 보여줍니다
  * Score History : 지인의 게임점수 히스토리를 그래프로 보여줍니다
  * Alarm : 지인의 게임점수가 변동되면 알람을 줍니다
  * Search History : 배틀그라운드 게임계정으로 지인을 검색합니다
  * Chat : 등록한 계정별로 대화방이 존재하며 해당 계정을 지인으로 등록한 사람들과 대화를 나눌 수 있습니다

## 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS CloudFront, Google Firebase

# OW Friends

![OwFriends](https://lh3.googleusercontent.com/DB7QF5c5b6n5uLWpCzGo8ZtWOavIVotAvvhgmLeGOL8wTv4AwU16s41QiNTrDlKtLMM=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.owfriends](https://play.google.com/store/apps/details?id=com.dveamer.owfriends)
  * 2017.02 ~ 2018.10 (기능 수정으로 인해 게시취소 중)
  * 다운로드 수 : 1,000 이상 (2017.11 기준)

오버워치(Over Watch) 게임 점수 갱신시 알림을 주는 앱입니다.  
원래는 회사 동료들이 게임에 접속하면 같이 접속하기 위해 만들기 시작했는데..  
원천적으로 불가능한 문제가 있어서 목적이 변경 된 앱이 탄생했습니다.  
지인의 게임 점수가 변동되면 알림을 주고 그 내용을 그래프로 기록해줍니다.  
회사 동료가 점심시간에 PC방을 갔는지, 지각을 했는데 어제 밤에 게임을 했는지 감시하고 놀려먹는 앱으로 변질됐습니다.  
나중에 회사 동료의 부주의로 상사에게 앱이 노출되어 피곤하다고 말도 못 꺼냈다는 슬픈 사연이 있습니다.  


## 제공하는 기능 

  * Score Board : 지인들의 게임점수를 한눈에 보여줍니다
  * Score History : 지인의 게임점수 히스토리를 그래프로 보여줍니다
  * Alarm : 지인의 게임점수가 변동되면 알람을 줍니다
  * Search History : 배틀그라운드 게임계정으로 지인을 검색합니다
  * Chat : 등록한 계정별로 대화방이 존재하며 해당 계정을 지인으로 등록한 사람들과 대화를 나눌 수 있습니다

## 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS CloudFront, Google Firebase

# Perfect Trainer

![Perfect Trainer](https://lh3.googleusercontent.com/S9jtmDIv013LFY971gFA36mOYoKleo5iu-GT6lT2BbDzSbCvsriG3L7D1IQ5ZZ5SJw=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer)
  * 게시기간 : 2017.02 ~ 2018.05
  * 다운로드 수 : 1,000 이상 (2017.11 기준)

등록해둔 포켓몬고(Pokemon Go) 몬스터가 주변에 나타나면 알람을 주는 앱입니다.  
망나뇽이 나타났다는 소식에 일하다 말고 회사동료들과 사무실 주변을 열심히 뛴적이 있었습니다.  
'망나뇽이 뭐라고 우리가 이렇게 뛰고 있지?'  
라는 의문이 들었지만, 그 순간 느꼈던 짜릿함은 잊을 수 없었습니다.  
회사에서 놀려고 만든건 아니고.. 그 짜릿함을 더 느껴보기 위해 만들었다는 핑계를 적어봅니다.  
의외로 자녀분들을 위해 희귀 몬스터를 찾아 떠나시는 과장님들이 선호했었습니다.  

## 제공하는 기능

  * Wanted List : 알람받고 싶은 몬스터들을 등록합니다
  * Found List : 설정해둔 거리 이내에서 나타난 몬스터들을 보여줍니다
  * Alarm : 설정해둔 거리 이내에서 몬스터들이 나타나면 알람을 줍니다
  * Showing Map : 찾은 몬스터의 위치를 지도에서 보여줍니다

## 사용된 기술

  * Front-End : Android Java
  * Back-End : Python3, AWS Lambda, AWS DynamoDB, AWS APIGateway, AWS Route53, AWS CloudFront

# Blind Date

![BlindDate](https://lh3.googleusercontent.com/DmgMzZ7DPwpxbFaxw06ydruPNyghpt8vJ_yKCo8ics6KQF9xeaZ6RG-awDKUHMqeiz0=w300){:class="imgTitle"}  

  * Google Play : [https://play.google.com/store/apps/details?id=com.dveamer.blinddate](https://play.google.com/store/apps/details?id=com.dveamer.blinddate)
  * 2015.08 ~ 2017.10
  * 다운로드 수 : 50 이상 (2017.11 기준)

사용자가 주선자가 되어 지인과 지인을 연결시켜주는 소개팅 앱 입니다.  
실제 오프라인 소개팅을 온라인으로 옮겨두었다고 보면 되지만 소개받은 지인들은 주선자가 누군지 알 수 없다는 것이 앱의 특징입니다.  
친구에게 소개팅을 시켜줬다가 결과가 좋지 않았던 것을 잊지 못하는 소심한 개발자는  
자신의 존재를 노출하지 않고 서로 잘 어울릴 것 같은 두 사람에게 대화의 기회를 만들어 주기 위해 앱을 만들었지만  
아무도 앱을 깔지 않아서 소개팅을 시켜주지 못했다는 슬픈사연이 있습니다.  

## 사용된 기술

  * 환경 : AWS VPC, EC2, Ubuntu
  * Front-End : Android Java
  * Back-End : Java, Socket, HTTP, Spring Boot Framework, Netty Framework, MariaDB, AWS APIGateway, AWS Route53
  * 구성 : Manager Server, Chatting Server, Push Server

 
