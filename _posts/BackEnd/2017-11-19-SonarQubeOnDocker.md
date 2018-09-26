---
layout: post
title:  "Docker를 이용해서 SonarQube 간단히 설정하기"
date:   2017-11-19 01:00:00
categories: BackEnd 
tags: BackEnd Setting SonarQube Docker
---

![SonarQube Logo](https://www.sonarqube.org/assets/logo-31ad3115b1b4b120f3d1efd63e6b13ac9f1f89437f0cf6881cc4d8b5603a52b4.svg){:class="imgTitle"} 
![Docker Logo](https://www.docker.com/sites/default/files/social/docker-facebook-share.png){:class="imgTitle"}  
(이미지 출처 : [https://www.sonarqube.org](https://www.sonarqube.org), [https://www.docker.com](https://www.docker.com))  


Docker를 이용해서 SonarQube를 정말 간단하게 설치하는 내용을 다룹니다.  
사실 명령어 한줄만으로 세팅이 완료되서 글을 작성하기 민망하지만.... (기록을 위해서!)  

<!--more-->

# 기존 설치방법 

깔끔한 OS 위에 처음부터 세팅하려면 아래와 같은 과정을 거쳐야합니다.

  * Java 설치, 환경설정
  * DB 설치
  * SonarQube에서 요구하는 DB table 생성
  * DB 계정 생성 및 권한설정
  * SonarQube 설치
  * SonarQube에 DB 정보 설정


이 글에서는 위의 내용을 다루지 않습니다.  
대신 위의 과정이 이미 모두 이뤄진 Docker image를 이용하는 방법을 설명합니다.  

# Docker를 이용해서 설치하기 

## 최초 설치 및 실행

~~~terminal
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube
~~~

sonarqube official repository 에서 이미지를 다운 받고 최초 실행시켜줍니다.  
sonarqube라는 이름으로 docker-image가 저장됐습니다.  

사실상 설치가 끝난 것이고 아래부터는 몇가지 사용법에 대해서 기술합니다.  

### License

SonarQube Docker 이미지는 [LGPL License](http://www.gnu.org/licenses/lgpl.txt) 를 가집니다.  

  * [LGPL License - 한글 위키백과](https://ko.wikipedia.org/wiki/GNU_%EC%95%BD%EC%86%8C_%EC%9D%BC%EB%B0%98_%EA%B3%B5%EC%A4%91_%EC%82%AC%EC%9A%A9_%ED%97%88%EA%B0%80%EC%84%9C)  
  

## SonarQube Container 시작/종료

이 내용은 SonarQube에 관련된 내용이 아니라 Docker container의 종료/조회/시작 하는 법입니다.  
PC 혹은 서버를 껐다켜서 container를 조회 후 내려가있다면 container를 시작시킨 후 다시 브라우저로 접속하시면 됩니다.  
container를 종료 후 시작시켜도 DB내용이 지워지는 것은 아닙니다.  

### Container 종료

~~~terminal
$ sudo docker stop sonarqube
sonarqube
~~~

### Container 조회

~~~terminal
$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                      PORTS                                            NAMES
80002e3a5bfc        sonarqube:latest    "./bin/run.sh"      7 days ago          Up 2 hours                  0.0.0.0:9000->9000/tcp, 0.0.0.0:9092->9092/tcp   sonarqube  
~~~

### Container 시작
~~~terminal
$ sudo docker start sonarqube
sonarqube
~~~

## 브라우저 접속

```localhost:9000``` 으로 접속해보시면 sonarqube 웹페이지에 접속됩니다.  
Container가 실행 된 후 DB, Sonarqube가 기동되는데까지 약간(1~2분)의 시간이 걸리므로 기다려야합니다.  

# 기본설정 

## Login Token 설정하기

![login-token](https://lh3.googleusercontent.com/-aAiwthIU7P5-IvQcJuFIcyymSE9t0MRqemPnqUrDNnzReebHA6Tsagv6WZvrAHQtvSIXf7eyTBVjNF52mjPUpQxSduLhYNmQuMW_Roy-hl6EjH9BohEaETAHZdoCI9Bx6bmtskzclEmKCCLJ1E_ah1wtvZGegPNIEbwuiuWcKjEgJTdHqdUE0eku6yW2tzk3NA0_Jgq-4UbPlHtONbbweQdIZpQYl4uXLX4rVyTB4bfm1hyK95liCVY38aod_HupcIKokx1Cn8MTPuHB143ownxff6u4TEUEQ_PAuUHgVgEUkKLTq58xh3JCGDKL4gDjNiYOO37-aFSJKVCPuqEkzMAuK5YINDGM--FQr74fTTqHwUV0Hi2L9Te9PS7OCyTAcQTaXVi9Fut-UnxbLjVmQucXuAQPiit-kTnCFlTpUJgRflQGM0-CtkyCU1azXrBBbd6vHAKgOv1liFTCrvQi5Z_5vJiigJ0d4XjeurLzTOtzlDOGnCA8hCEW6xjPmagiqen5vnMhLIyo3L9juTqHQfjFC3kG9Uycmjm8XvnafXXv8clRLRCiXFDl2WXb3emUzElUBgXViT8SUnqlDLhtUY9HWiT9UAQim2Dsy5YPC9w6iSvx112tEExCFkUXTBIvRHCAogyqElXJjP8JJgqfIpJHY-L0P7CeQ=w1545-h980-no)

위와 같은 순서로 진행하면 login-token이 생성됩니다.  
그리고 login-token을 maven, gradle에 설정을 하면 ID, Password 필요없이 SonarQube에 소스코드를 올릴 수 있습니다.  
SVN, Git 같은 형상관리에 ID, Password 가 commit 되는 상황을 방지할 수 있습니다.  

~~~gradle
property "sonar.login", "cc7876988f5e0fwejd69875b360b42ed34db4215"
~~~


## Project 설정하기 

![project](https://lh3.googleusercontent.com/QvsGTsycdNUA2AK9dtCuIC4_-T3ykAZDXFxGFKDrERbsdMRIvrw5spAygSsi030QBGYBAgz6b7SKRdJzBkg-L8_Aa0nmxD89W7WN_SClNfr23RMnxGBK51Cukn5SKsNX_8JPbH5Ws9AC4rRbx7p8UqptO5DI2Z3nq_1ciXTHvuCIwJ_0W8zWTUL5fiTfT9K4q6qcyqzfNiSGf4GHBSmRyJtpcV1sE04lp6vcQQSm7uxkmmBWwnj08tVKaDjmD3Kg0AnqZul73JjwJzkAKrxvVeqcFBkERMNlyQD_oiU1pteYHMfBxfFwRp6th8zIE-LZkCM98nnV0P6QAndMqNpj2HyCdgYZDbYctWRjcSJpH0HyzyOdWE0XzZiDOqY4E_LEzwPS5i80hL5KCdX8QG21vL4Bt5W11obS-J8zlMJ2GNrFphfH-OMvur0NKRCa9YFk2QBSUAQNV_7CgwG3_Cf4f5ndM_iJRP2LqUe7sI-CQnADoFps0ni3JJjNnNgdLBTNjW9-LPgJ_TyOmgL97IsOOj-RB1e5e8kGedYV5UuTU7fIIllPPwQpk8ycbBP4Dhbib7RbY80Jrtyz6VxSBTHtt7VyixVPU20LwigUaFfVKzjQtcgioeoRG8RDdbUigZpx6GRXiBN5QxCcZ7ogPT-8TJainm027V3sXw=w1543-h980-no)

위와 같은 순서로 진행하면 project가 생성됩니다.  
그리고 maven, gradle에 project 정보를 설정하면 해당 project로 소스코드가 올라가게 됩니다.  

~~~gradle
property "sonar.projectKey", "sonar:PUBGLog"
property "sonar.projectName", "PUBGLog"
~~~

## 소스코드 올리기

소스코드를 올려야 SonarQube가 분석을 시작합니다.  
올리는 방법은 Maven 혹은 Gradle 를 이용한 방법도 있고 Jenkins 를 이용한 방법도 있습니다.  

이에 대해서는 해당 글에서 다루지는 않습니다.  

해당 설정에 관해서는 다른 글에서 다룰 예정입니다. 관련 tag 글을 참고하세요.  

  * [Android 소스코드 SonarQube 연동](/android/SonarQubeForAndroid.html)


## 외부 DB 연동하기

위에서 설치한 SonarQube container는 "embedded H2 database" 를 가지고 돌아갑니다.  
내장된 DB가 아닌 외부 DB와 연동해야 되는 경우에는 Docker Run 하실 때 DB 접속정보를 함께 입력하시면 됩니다.  

  * 대규모 프로젝트에서 사용 될 경우 
  * SonarQube 분석결과, 룰셋 등이 이미 외부 DB에 축적되어있을 경우 

~~~terminal
$ docker run -d --name sonarqube \
    -p 9000:9000 -p 9092:9092 \
    -e SONARQUBE_JDBC_USERNAME=sonar \
    -e SONARQUBE_JDBC_PASSWORD=sonar \
    -e SONARQUBE_JDBC_URL=jdbc:postgresql://localhost/sonar \
    sonarqube
~~~

#### References

  * [Docker Offical Repository - sonarqube](https://hub.docker.com/_/sonarqube/)


