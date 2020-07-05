---
layout: post
title:  "Docker 설치 후 이미지 보관 디렉토리 변경"
date:   2018-06-18 00:00:00
categories: BackEnd
tags: BackEnd Setting Docker
---

![Docker Logo](https://www.docker.com/sites/default/files/social/docker-facebook-share.png){:class="imgTitle"}  
(이미지 출처 : [https://www.docker.com](https://www.docker.com))  

Docker를 설치하고 난 뒤 반드시  
Docker image, container 등의 정보가 쌓이는 디렉토리의 위치를 변경해줘야 합니다.  

기본설정을 유지하면 나중에 root(/) 가 꽉차는 불상사를 당할 수 있습니다.  

<!--more-->

# Docker Installation

설치방법은 공식 [Docker 페이지](https://docs.docker.com/install/)에서 확인을 부탁드립니다.  

예전에 다른 글에서 설치방법을 적었었는데  
Docker 버전이 올라가면서 설치방법이 바뀌더군요.  
적어놓더라도 의미가 없을 것 같아서 링크를 남깁니다.  

참고로 Docker는 EE(Enterpise Edition)와 CE(Community Edition)가 있습니다.  
EE를 설치 하기 위해서는 Docker 사이트에 가입하고 문의 후 trial URL을 받아야 합니다.  

저의 경우에는 Ubuntu에서 apt-get 으로 CE를 설치 후 진행했습니다.  
인터넷이 안되는 환경에서는 [binary 설치](https://docs.docker.com/install/linux/docker-ce/binaries/) 방법으로 진행 가능합니다.  
 

EE와 CE의 기능차이는 아래링크에서 확인하시면 됩니다.  

  * https://docs.docker.com/ee/supported-platforms/

# Docker 이미지 보관 디렉토리

Ubuntu/Debian의 경우 ```/var/lib/docker```에 docker image, container 등의 정보가 쌓입니다.  
root(/)가 꽉차게 되면 문제가 될 수 있으니 root(/)가 아닌 다른 path에 정보가 쌓이도록 변경해야합니다.  

Symbolic-link를 이용한 방법도 있지만 docker 옵션파라미터를 이용하는 방법으로 진행하겠습니다.  

## 변경 전 확인

~~~terminal
$ sudo lsof | grep /var/lib/docker
~~~

```/var/lib/docker``` 디렉토리에 위치한 여러파일들을 dockerd, docker-co 프로세스들이 사용중인 것을 확인할 수 있습니다.  
참고로 sudo 를 써주서야 확인이 가능합니다.  

## Docker 프로세스 중지

~~~terminal
$ sudo service docker stop
~~~

## 디렉토리 생성

Docker image, container 정보들을 보관할 디렉토리를 생성합니다.  
임의로 ```/data/docker_dir``` 라고 정해서 진행하겠습니다.  

~~~terminal
$ mkdir -p /data/docker_dir
~~~

## Docker_OPTS 변경 

```/etc/default/docker``` 파일을 열어서 DOCEKR_OPTS 에 ```-g``` 옵션을 추가합니다.  

~~~vim
# vi /etc/default/docker
DOCKER_OPTS="-g /data/docker_dir"
~~~

Fedora/Centos의 경우 ```/etc/sysconfig/docker``` 파일을 열어서 ```other_args```를 수정하시면 됩니다.  


#### References

  * [How do I change the Docker image installation directory?](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)

<!--ads-->

## 주의사항 

Ubuntu와 Windows를 멀티부팅으로 사용하는 노트북에서 테스트를 하다가 우연히 에러에 부딪혔습니다.  

docker 디렉토리를 변경 전에는 정상적이었는데  
docker 디렉토리를 변경 후 container를 기동하는 과정 중에 발생한 에러입니다.  

~~~terminal
docker: Error response from daemon: error creating overlay mount to /data/docker_dir/overlay2/e1c62b0a2633cbbe1ae9ad587f163c29a8e270ca307dc71891503572567a1c89-init/merged: invalid argument.
See 'docker run --help'.
~~~

입력한 옵션을 다 빼봤지만 동일한 에러가 발생했고  
docker를 지운 후 다시 설치해봤지만 동일한 에러가 발생했습니다.  

알고보니 제가 mount해둔 ```/data``` 디렉토리가 Windows와 Ubuntu의 자료를 공유할 목적으로 NTFS 포멧으로 파티션을 생성했습니다.  

다른 파티션으로 docker 디렉토리를 변경해보니 정상적으로 되는 것을 확인했습니다.  
파티션 포멧에 따라 overlay2 관련해서 에러가 발생할 수 있는 것으로 추측됩니다.  

## Docker 프로세스 시작

~~~terminal
$ sudo service docker start
~~~


## 변경 후 확인

~~~terminal
$ ps -ef | grep docker
~~~

docker 프로세스 실행명령어를 보면 ```-g``` 옵션이 들어간 것을 확인할 수 있습니다.  

~~~terminal
$ sudo lsof | grep /var/lib/docker
$ sudo lsof | grep /data/docker_dir
~~~

```/var/lib/docker``` 디렉토리에 access하고 있는 프로세스는 없고  
```/data/docker_dir```디렉토리에 위치한 여러파일들을 dockerd, docker-co 프로세스들이 사용중인 것을 확인할 수 있습니다.  

~~~terminal
$ du /data/docker_dir/ -sh
~~~

```/data/docker_dir``` 디렉토리 용량을 체크하는 것으로도 확인 가능합니다.  
새로운 이미지를 pulling하거나 컨테이너를 띄우면 디렉토리 용량이 변하는 것을 확인 가능합니다.  


# 백업 & 복구

```/data/docker_dir``` 해당 디렉토리를 특정시점에 백업해뒀다면  
Docker를 지우고 새로 설치하더라도 이미지 보관 디렉토리 설정을 ```/data/docker_dir```로 다시 해주면  
해당 시점의 image와 container 들을 복구할 수 있습니다.  

올바른 Docker 사용법이 아닐 수는 있어도 서비스를 운영하는 입장에서는 쉬운 백업 방법 중 하나입니다.  

다른 OS에서 위와 같은 복구가 가능한지는 테스트해보지 않았습니다. 



