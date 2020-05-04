---
layout: diary
title:  "나의 첫 Pull Request : Spring Cloud Gateway"
date:   2020-05-05 00:00:00
categories: Diary
tags: Diary
---

![FirstPullRequest](/images/post_img/FirstPullrequest/MyFirstPullRequest.png)  
[Dveamer`s GitHub](https://github.com/dveamer)  

나의 첫 pull request를 보냈다.  
그리고 얼떨결에 [Spring Cloud Gateway](https://github.com/spring-cloud/spring-cloud-gateway) contributor가 되어버렸다.  

<!--more-->

오픈소스에 기여할 일이 생길거라고는 생각해본적이 없는데 우연찮게 기회가 찾아왔다.  
Spring Cloud Gateway의 다른 기능들에 대해서 좀 더 알아보기 위해서 공식문서를 살펴보고 있었다.  
[RewritePath GatewayFilter](https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.2.RELEASE/reference/html/#the-rewritepath-gatewayfilter-factory)의 샘플 내용을 예전에 시도해보다가 실패한 적이 있었다.  
근데 그 때는 Spring Cloud Gateway를 처음 접했던 때이기도하고 당시에 급하게 작업을 진행하고 있었기 때문에 RewritePath GatewayFilter를 사용하지 않고 다른 방법으로 처리를 했다.  

근데 이번에는 휴일이고 집이라서 여유로운 상태였기 때문일까..  
아니면 Spring Cloud Gateway로 프로젝트를 구축하고 오픈한 경험 덕분일까..  
공식 문서에 적힌 예제가 잘못됐다는 것이 보였다.  

테스트를 통해 확인 후 바로 fork를 받고 수정해서 pull request를 보냈다.  
의도한 것은 아닌데 pull request 번호도 1700번으로 참 예쁘게 받았다.  

![PullRequest](/images/post_img/FirstPullrequest/PullRequest.png)  

[pull request #1700](https://github.com/spring-cloud/spring-cloud-gateway/pull/1700)  

그리고 몇일이 지나고 pull request는 받아들여졌고 반영됐다.  
굉장히 단순한 내용이라서 몇일 걸릴 내용이 아닌데 내가 하필 금요일 노동절(2020.05.01)에 pull request를 올렸고 maintainer들도 쉬느라 다음 월요일 밤이 되서야 진행이 된 것 같다.  
"쉬는 날이여서 처리 안해주는 거겠지? 혹시 내가 착각하고 있는게 있나?"라는 생각이 주말기간동안 머리속에서 떠나질 않아서 쉽지 않았다.  

위의 이미지를 보면 내가 작성한 comment의 우측 상단에 Contributor라는 문구가 생겼다.  
그리고 [contributors](https://github.com/spring-cloud/spring-cloud-gateway/graphs/contributors)에 Dveamer가 표시되어있다.  

<br>

Commit한 내용은 굉장히 단순하다. 어쩌면 지금까지 contributor 중에서 가장 짧은 수정을 했을지도 모른다. 딱 3글자 수정했다.  

![Commit](/images/post_img/FirstPullrequest/Commit.png)  

[commit](https://github.com/spring-cloud/spring-cloud-gateway/commit/dea0da56338f65488853edb24255f472fbfb6fd0)

<br>

지금 내 느낌을 적어보자면,  
일단 contributor 명단에 이름을 올리게 되어 기쁘다. 특히 Spring Cloud Gateway라서 더 기분좋다.  
이름 있는 오픈소스이기 때문도 있지만  
Spring Cloud Gateway를 최근 프로젝트에서 사용했었고  
지금 새로 진행하고 있는 프로젝트의 요구사항도 모두 충족하는 것으로 판단되어 자체 개발이었던 현 계획을 수정하고 Spring Cloud Gateway를 사용하자고 제안할 예정이었다.  
더 애착이 생겨서 앞으로 Spring Cloud Gateway의 장점을 열심히 찾고 전파해나가는 것이 아닐지 모르겠다.  

그리고 코드 수정만이 아니라 오타 수정과 같은 사소한 기여만으로도 contributor 명단에 이름을 올릴 수 있다는 것은 이미 알고 있었다.  
하지만 정말로 가이드 문서의 예제에 오류를 정정한 것만으로 contributor 명단에 이름을 올리게 될줄은 몰랐다.  
운이 좋았고 덕분에 재미있고 흥분되는 이벤트였다.  

또 운이 따라줄지는 모르겠지만, 더 많은 [commits](https://github.com/spring-cloud/spring-cloud-gateway/commits?author=dveamer)을 올릴 기회가 있으면 좋겠다.  
그리고 가능하다면 이번에는 코드 수정을 통한 기여를 해보고 싶다.  

