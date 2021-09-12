---
layout: post
title: "Docker 빌드 성능 개선 : dockerignore, node_modules"
date: 2021-08-14 00:00:00
lastmod: 2021-08-14 00:00:00
categories: BackEnd Docker CICD 
tags: BackEnd Docker CICD
hidden: false
published: true
---

"도커 빌드가 예전에는 빨랐는데.. 요즘 왜 이렇게 느리지?" 라는 생각이 드신다면 이 글이 도움이 될 수 있습니다.  

<!--more-->

## Build Context 내의 고용량 파일 혹은 다수의 파일 

Docker 빌드시 ㅣ**Sending build context to Docker daemon 100.38 MB** 와 같은 내용의 로그가 남을 것입니다.  

~~~bash
$ docker build -t dveamer/sample
Sending build context to Docker daemon 100.38 mB
Sending build context to Docker daemon 
Step 0 : FROM ubuntu:20.04
 ---> 6dc9a70f4083
Step 1 : MAINTAINER Dveamer "dveamer@gmail.com"
 ---> Using cache
 ---> 8f047119d17a

...(생략)

~~~

Dockerfile이 위치한 디렉토리와 그 하위의 모든 파일들이 docker daemon 으로 전송됩니다.  
이 과정에서 파일의 개수가 많다거나 큰 용량의 파일이 있다면 시간을 꽤나 잡아 먹습니다.  

모든 환경에서 발생하는 것은 아닌 것 같습니다.  

Ubuntu 환경에서 느려지는 것은 목격했고  
Linux host OS 위에 GitLab Runner로 docker on docker로 docker 빌드를 진행할 때는 굉장히 느려지는 것을 체감했습니다.  

근데 동일 dockerfile을 가지고 Windows 환경에서 빌드 할 때는 느리지 않아서 원인을 찾는데 굉장히 힘들었습니다.  
또한 Windows 서버로 CI/CD 환경을 구성한 지인의 이야기를 들어보면 크게 느리지 않다고 들은 바 있습니다.  

docker build를 자주하는데 너무 느려진다면 한번쯤 의심해보시기 바랍니다.  
 
문제는 **Sending build context to Docker daemon 100.38 MB**와 같은 문구가 출력되지 않는 상황입니다. 저의 경우는 GitLab Runner 로그에 해당 문구가 남지 않아서 느려진 원인을 몰라 해결이 굉장히 어려웠었습니다.  


### 해결방법

**.dockerignore** 파일을 만들어서 불필요한 파일들을 docker context에서 제외 되도록 처리 합니다.  
즉, Dockerfile에서 사용하지 않는 파일들은 **.dockerignore** 에 모두 작성하시면 됩니다.  

예를들어, 크게 영향을 주는 것이 **.git** 디렉토리 입니다.  
용량은 크지 않으나 내부적으로 디렉토리 파일들을 굉장히 많이 갖고 있어서 영향을 많이 줍니다.  

컴파일 된 결과물만 필요하다면 컴파일 전 소스, 가이드 문서 등은 제외해버리시면 됩니다.  

~~~bash
$ docker build -t dveamer/sample
Sending build context to Docker daemon 461.8 kB
Sending build context to Docker daemon 
Step 0 : FROM ubuntu:20.04
 ---> 6dc9a70f4083
Step 1 : MAINTAINER Dveamer "dveamer@gmail.com"
 ---> Using cache
 ---> 8f047119d17a

...(생략)

~~~

불필요한 파일들을 모두 **.dockerignore**에 작성하고 다시 docker 빌드를 돌렸더니 100mb가 461kb 로 줄어든 것을 볼 수 있습니다.  

**.dockerignore** 작성법은 [docker 공식 사이트의 설명](https://docs.docker.com/engine/reference/builder/#dockerignore-file)을 참고하시기 바랍니다. 참고로 **.gitignore**랑 작성법이 같다고 보시면 됩니다.  


## node_modules

node.js 계열이라면 난감합니다. 컴파일 후 생성된 node_modules 디렉토리를 docker image에 담아야하니 **.dockerignore**로 처리할 수는 없는데 node_modules 디렉토리 내의 파일 개수는 세는게 무의미할 정도고 용량은 쉽게 1G를 넘어갑니다.  

이 때는 1~2분정도의 시간이 아닌 20~30분 단위의 큰 시간이 소요됩니다.  

### 해결방법 

이 때는 (잘 될지는 모르겠으나) 빌드서버를 Linux가 아닌 다른 OS 환경의 서버로 변경해보시는 것도 답일 것 같습니다.  

저의 경우는 node_module을 이미 포함하고 있는 base_node라는 docker image를 만들어놓고 application 빌드시에는 base_node 위에 레이어를 쌓는 형태로 진행했습니다. 대신 package.json과 같은 의존성이 변경되는 상황에서는 base_node도 재빌드가 필요하고 시간이 꽤나 걸립니다. 게다가 개발, 스테이지, 운영 배포를 위한 base_node 이미지 태그관리도 필요하실 겁니다.  


