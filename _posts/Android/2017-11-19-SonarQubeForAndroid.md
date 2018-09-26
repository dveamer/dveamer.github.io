---
layout: post
title:  "SonarQube 로 Android 소스코드 분석하기"
date:   2017-11-19 01:00:00
categories: Android 
tags: Android Setting SonarQube
---

![SonarQube Logo](https://www.sonarqube.org/assets/logo-31ad3115b1b4b120f3d1efd63e6b13ac9f1f89437f0cf6881cc4d8b5603a52b4.svg){:class="imgTitle"} 
![Android Logo](https://developer.android.com/_static/images/android/touchicon-180.png){:class="imgTitle"}  
(이미지 출처 : [https://www.sonarqube.org](https://www.sonarqube.org), [https://developer.android.com](https://developer.android.com))  

Android 소스를 SonarQube를 통해서 정적분석을 하는 방법을 설명합니다.  

<!--more-->

# 설정

## SonarQube 설치하기

  * [Docker를 이용해서 SonarQube 간단히 설정하기](/backend/SonarQubeOnDocker.html) 글 참고

## app/build.gradle 설정

```app/build.gradle``` 파일에 아래 내용을 추가 합니다.  

~~~gradle
apply plugin: "sonar-runner"

sonarRunner {
      sonarProperties {
         property "sonar.host.url", "http://localhost:9000" 
         property "sonar.sources", "src/main/java"
         property "sonar.java.binaries", "build/intermediates/classes/release"

         property "sonar.login", "cc7876988f5e0fwejd69875b360b42ed34db4215"
         property "sonar.projectKey", "sonar:PUBGLog"
         property "sonar.projectName", "PUBGLog"
      }
}
~~~

  * sonar.host.url 
    - localhost로 잡혀있습니다. SonarQube가 다른 서버에 설치되어있다면 해당 서버의 IP 혹은 도메인을 설정하시면 됩니다. 방화벽 확인하시구요.  
  * sonar.login  
    - ID, Password 대신 token 값으로 로그인 가능합니다.  
    - 해당 값은 예시일 뿐이고 SonarQube 웹페이지에서 생성한 token 값을 넣어주셔야 합니다.  
    - "[Login Token 설정하기](/backend/SonarQubeOnDocker.html#Login Token 설정하기)" 내용 참고  
  * sonar.projectKey, sonar.projectName
    - SonarQube에는 여러개의 프로젝트 소스코드를 올릴 수 있습니다.  
    - SonarQube 관리자와 협의한 프로젝트 정보로 설정하시는 것을 추천 드립니다.  
    - "[Project 설정하기](/backend/SonarQubeOnDocker.html#Project 설정하기)" 내용 참고  

# 실행

~~~terminal
$ gradle sonarRunner
~~~

위 커맨드만 입력하시면 SonarQube로 소스업로드 후 분석이 진행됩니다.  
웹페이지로 접속해서 분석된 결과를 확인하시면 됩니다. 이제.. 분석된 결과를 가지고 소스 수정만 하시면 되네요.  


