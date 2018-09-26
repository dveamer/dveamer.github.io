---
layout: post
title:  "Linux에서 컴파일 설치법을 알아야하는 이유"
date:   2018-02-03 00:00:00
categories: BackEnd
tags: BackEnd Setting Linux
---

![GCC Compiler](https://gcc.gnu.org/img/gccegg-65.png){:class="imgTitle"}  
(이미지 출처 : [https://gcc.gnu.org](https://gcc.gnu.org))  

Linux에서 뭔가 설치하려고 인터넷을 검색해보면  
컴파일 설치 방법에 대해서 가이드가 득실득실 합니다.  

Debian계열의 Ubuntu 에서는 ```apt-get```가 있고  
CetnOS, RHEL 에서는 ```yum``` 이라는 편한 package-manager가 있는데..  
굳이 왜 컴파일해서 설치하는 법을 알아야할까요?  

<!--more-->

# Package Manager란?

Debian계열의 Ubuntu 에서는 ```apt-get``` 으로 프로그램 설치, 업데이트, 삭제가 가능합니다.  
CetnOS, Redhat 에서는 ```yum``` 을 이용하면 됩니다.  
NPM(Node.js Package Manager)처럼 개발언어 계열의 package manager도 있습니다.  

package manager는 프로그램을 설치할 때 의존성있는 프로그램들도 같이 설치해주고  
OS 종류, 버전에 맞춰서 설치까지 해줍니다.  

다만 pacakge manager도 결국 컴파일 설치와 동일합니다.  
해당 OS 종류, 버전 그리고 타 프로그램과의 의존성에 맞추서  
소스를 다운받아주고 컴파일해주는 역할을 합니다.  
혹은 이미 컴파일된 파일을 다운 받아주는 역할을 합니다.  

# 컴파일 설치법을 알아야하는 이유

기술적인 문제는 아니고  
작업환경이 package manager를 사용할 수 없는 경우가 많기 때문입니다.  

집에서 개인적인 목적으로 설치를 한다면 package manager를 사용하면 됩니다.  

하지만 회사에서 업무적인 목적으로 사용한다면,  
Package manager 사용이 원활하지 않을 수도 있습니다.  

그렇다고 회사들이 이상한 것은 아닙니다 ^^;  

대부분 보안정책의 이유일 것입니다.  

Packager manager 사용자체가 막혀있을 수 도 있고  
서버관리자 개발자 등의 역할 분할로 개발자에게는 root 권한이 없을 수도 있습니다.  
자신이 서버관리자여서 root 권한이 있다할지라도 해당 서버가 private-zone에 위치해서 인터넷 연결이 안될 수도 있습니다.  
다 되더라도 DNS설정 문제로 신규 repository에 접근이 안될 수도 있습니다.  

나름대로 이유가 있고 그 이유에 비하면  
package-manager를 사용하지 못해서 설치, 보안패치에 대한 불편함은 무시될만도 합니다.  


## 컴파일 설치법이 인터넷에 득실득실한 이유

이건 제 추측인데..  

기술 블로그를 운영하는 사람들은 대부분 나중에 참고하기 위해서 기록을 목적으로 할 것입니다.  
다양한 곳에서 다양한 이유로 프로그램을 재설치할 일이 많은 사람들인거죠.  

다양한 곳에서 일을 하다보면  
다양한 보안정책을 마주했을 겁니다.  
하지만 컴파일을 통해 설치하는 방법을 기록해 둔다면 어디서든 써먹을 수 있겠죠.  

결국  
가장 손이 많이가고 버전에 대해서 고민해야하는 방법이지만  
직접 컴파일해서 설치하는 가장 보수적인 방법이  
어디서나 사용할 수 있는 가장 편한 방법이 되어버린 것입니다.  

게다가 package manager를 이용한 설치방법은 너무 간단해서 기록이 필요없을 경우가 많습니다.  

# 설치시마다 매번 컴파일 해야하는 이유

OS를 RHEL 5.x에서 RHEL 6.x 로 변경했습니다.  
RHEL 5.x에서 사용하던 프로그램의 디렉토리를 복사해왔는데 에러가 납니다.  

왜 그럴까요?  

간단하게 설명해보면  
OS의 버전이 변경되면서 OS와의 interface가 변경이 있었을 것이고  
복사해온 프로그램은 예전 OS interface에 맞춰져있기 때문에 에러가 날 것입니다.  

만약 OS의 종류, 버전이 동일하다면 복사해온 프로그램이 에러가 나지 않을 수도 있겠죠.  
하지만 다른 프로그램과 의존성이 있는 프로그램이라면 비슷한 이유로 문제를 일으킬 수 있습니다.  

<br> 

다른 서버에서 yum을 통해 pakcage를 다운로드 받고 SFTP로 옮긴 후 yum을 통해 설치하면 설치가 되는 경우가 있습니다.  

package managr로 설치하는 것은 현재 환경에 맞게 컴파일된 결과물을 다운받아서 설치해주는 것입니다.  

즉, 다른 서버에서 yum을 통해 다운받은 package는 다른 서버의 환경에 맞춰서 받아온 package 일뿐입니다.  

OS 종류, 버전이 모두 동일하고  
의존성 있는 타 프록르매들의 버전이 모두 동일한 경우 문제가 일어나지 않을 겁니다.  
하지만 조금의 차이라도 있다면 문제가 발생할 가능성이 있습니다.  

## OS Image로 복사하기

Docker를 사용한다거나  
AWS, GCP 같은 cloud 환경에서 작업을 한다면 매번 컴파일하는 작업은 피할 수 있습니다.  

필요한 것을 설치해둔 OS를 통째로 image로 만들어서 매번 OS 통째로 복사해서 쓰면 됩니다.  
이것은 꼼수같은 방법이 아니라 적극 추천해드리는 방법입니다.  

# 컴파일 설치가 불필요한 대상

Linux에서 모든 프로그램이 컴파일 설치가 필요한 것은 아닙니다.  
간혹 압축만 풀어서 사용하면 되는 프로그램들이 있었을 겁니다.  

예를들어 [Tomcat](http://tomcat.apache.org/)과 같은 프로그램은 다운받아서 압축만 풀면 됩니다.  

그 이유는 Tomcat은 JVM 위에서 돌아가기 때문입니다.  
대신 JRE 혹은 JDK가 미리 설치 되어있어야 하죠.  

OS 종류, 버전 등의 환경차이가 있다하더라도 OS와 Tomcat의 중간에서 JVM이 알아서 처리해줍니다.  


# 기본적인 gcc 컴파일 설치 방법

gcc 컴파일러는 C언어로 짜여진 프로그램을 컴파일해주는 툴입니다.  
gcc-c++이라는 C++언어의 컴파일러도 있습니다.  

## Download

~~~terminal
$ mkdir -p /tmp/{program_name}
$ cd /tmp/{program_name}
$ wget {program_download_url}
$ tar zxvf {program_file_name.tar.gz}
~~~

```/tmp/{program_name}``` 이라는 임시디렉토리를 생성하고  
프로그램 소스 다운로드 받고 압축 풀고 컴파일 하는 과정입니다.  

아래 compile 작업을 모두 끝낸 다음에  
```/tmp/{program_name}``` 임시디렉토리는 삭제하시면 됩니다.  

## Compile 

~~~terminal
$ cd {program_file_name}
$ ./configure --prefix=/usr/local/{program_name}
$ make
$ make install
~~~

  * configure : 옵션 입력을 통해 설치를 위한 정보를 입력합니다. 입력하지 않으면 default 정보를 가지고 설치가 이뤄집니다.  
    * --prefix : 프로그램이 설치될 디렉토리 설정을 위한 옵션입니다.  
  * make : 컴파일를 위한 준비작업을 진행합니다.  
  * make install : 컴파일을 진행합니다.  

컴파일 과정은 프로그램마다 다릅니다.  
하지만 대부분의 큰 줄기는 비슷합니다.  

```make```, ```make install``` 은 gcc, gcc-c++ 에서 제공하는 명령어라서 거의 변동이 없습니다.  
간혹 ```make``` 앞에 ```make depend``` 같은 명령어가 추가수행이 필요한 경우는 있습니다.  

configure는 프로그램별로 사용법이 다릅니다.  
configure가 아니라 config일 수도 있고  
설치정보를 옵션이 아닌 환경변수 추가를 통해 전달할 수도 있습니다.  



