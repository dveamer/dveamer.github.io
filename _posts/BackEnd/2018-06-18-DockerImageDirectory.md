---
layout: post
title:  "Python WAS 구축하기 ( Django, Nginx, Gunicorn )"
date:   2018-06-25 00:00:00
categories: BackEnd
tags: Setting Python
---



![Django](https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Django_logo.svg/278px-Django_logo.svg.png){:class="imgTitle"} ![Gunicorn](http://gunicorn.org/images/logo.jpg){:class="imgTitle"}  

( 출처 : [Wikipedia-Django](https://en.wikipedia.org/wiki/Django_(web_framework)), [Gunicorn](http://gunicorn.org/) ) 

Python으로 REST API 서비스를 위한 WAS(Web Application Server) 구축을 진행합니다.  

Django만으로도 REST API를 오픈할 수 있지만  
Django의 runserver는 단순히 테스트만을 위한 기능으로  
운영환경에서 사용하면 성능상 문제를 겪게 됩니다.  

운영모드에서 [Gunicorn](http://gunicorn.org/) 같은 WSGI(Web Server Gateway Interface) 미들웨어와 연동이 필요합니다.  


![WSGI Middleware](https://lh3.googleusercontent.com/HsVbgKmzo36XJWxW-m33OQinSAwKfR1UyG1STM16WGPgtzhIyj7XkPNlvPqz8x7oYubWrclDH7bHsDU8Q84f1cnG1hoxgK2UyPGrqW4nyxfdF3M_daKpDwuEJoE4l8Stv35rA9MEBQ1Qq8V4GLtzkYQyQIiKkEWJI4mHds4_IsrFMhh2ohd1Dwpa6SWcwr9JthOu9cIK9MbWvI6HjohZUmEGkAUrUqNYG2PbtIbLpMVIeunUCqo2dnLs5JVQTTHNpormp-WPbNLJjgJ0Rh1BYmP6Uh-LAao3w9q7vSMCErQ1Hjl2ir31tZP1y2XxDRj-WE00hlDW-3NFFc15rNl8nvSLNB8x8J5Bqfi3Ks9fK7VxVoTzlbhvuT92ReuZyxSHxtIOWq9ruKEgmLbMp6xb7v0rcIDCAcxGwbIcsWXzpTj7nPtjo9zH2MQd7kxWsFgkU7Yt1YsKJanLoaoZrirfFGaCtZjIs6EGdwn9Fl0M5IoVYLK8je1kbyuVTucJ10Tknyd_9XrDzsFxaH0arJ3xZGowjlGmGgymn4k3VcOtzri6TGkrN6KpO-ORBcwlb04XreQ2s54KiLLme3ZgS6wVPkc9SBs6q9faZS8OilVDCCmmgJIsFXvn-4OH6jPftSbbeW82Ubj1zW2h3K64sta3rZqyl4-e-gly=w886-h308-no)

<!--more-->


위와 같은 구조에서 ```Private Physical Server``` 위에 놓여진 것들을 구성하는 것이 이번 글의 목표입니다.  
즉, Linux 환경에서 Nginx, Gunicorn, Django를 연동하는 과정을 보여드리겠습니다.  

Python, virtualenv, Django, REST API 에 대해서는 설명없이 설치, 설정 방법을 기술할 예정이며   
WSGI에 대해서 간략히 설명 후 Nginx, Gunicorn 설치, 설정을 설명하겠습니다.  


Django와 REST API 에 대한 설명은 다른 글에서 진행하도록 하겠습니다.  
그리고 Celery에 대해서도 글을 작성할 예정입니다.  

# 환경 구성

## 작업환경

  * CentOS 7

## 구성내역

  * Python 3.6
  * Nginx
  * Gunicorn 
  * Django

## 필요 프로그램 설치

~~~terminal
$ yum install gcc gcc-c++ 
$ yum install zlib-devel
$ yum install openssl openssl-devel
$ yum install sqlite sqlite-devel
~~~

위의 프로그램들이 설치되지 않은채로 Python이 컴파일 됐다면 나중에 문제가 생길 수 있습니다.  
예를들어 sqlite-devel이 설치되어있지 않다면 sqlite를 사용하는 시점에 라이브러리를 찾을 수 없다는 에러가 발생합니다.  
필요한 프로그램들을 설치 후 Python을 재컴파일 하셔야합니다.  

## User, Group 생성

~~~terminal
$ groupadd g_news 
$ useradd -g g_news u_news 
$ passwd u_news
~~~


# Python 환경구성

## Python 3.6.5rc1 컴파일 설치

브라우저로 ```https://www.python.org/ftp/python``` 에 접속해보면 Python 소스가 버전별로 정리되어있습니다. 필요한 버전을 설치하시면 됩니다.  

~~~terminal
$ mkdir /tmp/python-install -p
$ cd /tmp/python-install
$ wget https://www.python.org/ftp/python/3.6.6/Python-3.6.6rc1.tgz
$ tar -zxvf Python-3.6.6rc1.tgz
$ cd Python-3.6.6rc1
$ ./configure --enable-optimizations
$ make altinstall
~~~

## Symlinks 설정

~~~terminal
$ cd /usr/local/bin
$ ln -s python3.6 python3
~~~

python3 파일이 이미 있다면 ```/usr/local/bin/python3```을 삭제 후 다시 symlinks 설정해주면 됩니다.  

## 확인작업

~~~terminal
$ python3 --version
Python 3.6.6rc1
~~~

## 불필요 파일 제거 

~~~terminal
$ cd /tmp
$ rm -rf ./python-install
~~~

# virtualenv

## virtualenv 만들기

~~~terminal
$ mkdir /engn001
$ cd /engn001
$ python3 -m venv news_venv
$ chown -R u_news:g_news nes_venv
~~~

## virtualenv 사용하기

~~~terminal
u_news$ su - u_news
u_news$ cd /engn001/news_venv/news
u_news$ . bin/activate
(news_venv) u_news$ 
~~~

# PIP를 이용한 패키지 설치

PIP를 이용해서 필요한 패키지를 설치할 수 있습니다.  
지금은 DJango와 DJango-Rest-Framework을 설치하겠습니다.  

## DJango-REST-Framework 설치

~~~terminal
(news_venv) u_news$ pip install django djangorestframework
~~~

## 그 외 필요한 패키지 설치

~~~terminal
(news_venv) u_news$ pip install serializers tree
~~~

```serializers``` 라는 패키지를 추가 설치했습니다.  
```serializers```는 데이터를 JSON, XML과 같은 native 데이터로 바꿔주는 역할을 합니다.  

이 외에도 필요한 Python 패키지가 있을 경우에는 pip 명령어로 추가 설치하시면 됩니다.



# Framework : DJango

## Sample 위치확인

CRUD 내용을 담은 REST API 샘플코드를 공유합니다.  
샘플코드를 수정해서 사용하실거면 아래의 "프로젝트 생성" 과정은 참고만하시고 생략하셔도 됩니다.  

## 프로젝트 생성

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv
(news_venv) u_news$ mkdir /engn001/news_venv/run
(news_venv) u_news$ django-admin.py startproject news
(news_venv) u_news$ cd /engn001/news_venv/news
(news_venv) u_news$ python manage.py startapp article
~~~

생성된 프로젝트의 구조를 확인해보겠습니다.  
(tree 프로그램이 없으시다면 yum으로 설치 가능합니다. )  

~~~terminal
(news_venv) u_news$ tree /engn001/news_venv/news
.
|-- article
|   |-- __init__.py
|   |-- admin.py
|   |-- apps.py
|   |-- migrations
|   |   `-- __init__.py
|   |-- models.py
|   |-- tests.py
|   `-- views.py
|-- manage.py
`-- news
    |-- __init__.py
    |-- __pycache__
    |   |-- __init__.cpython-36.pyc
    |   `-- settings.cpython-36.pyc
    |-- settings.py
    |-- urls.py
    `-- wsgi.py
~~~

### Settings.py 수정

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv/news/news
(news_venv) u_news$ vi settings.py
~~~

INSTALLED_APP 리스트에 ```rest_framework```은 필수로 등록하고 아까 생성한 app의 이름을 등록해줍니다.  

~~~vim
INSTALLED_APPS = [
    ...
    'news'
]
~~~

문서 하단에 아래 내용을 추가합니다.  
Admin만 REST_FRAMEWOKR 페이지를 사용하게 하려면 아래 주석을 제거하시면 됩니다.  

~~~vim
# REST_FRAMEWOK
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        # 'rest_framework.permissions.IsAdminUser',
    ],
    'PAGE_SIZE': 10
}
~~~

## Rest API 구성

  * https://www.slideshare.net/ridwanbejo/resftul-api-web-development-with-django-rest-framework-celery
  * http://www.django-rest-framework.org/api-guide/viewsets/
   
    
### Pagination

## DB Migration

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv/news
(news_venv) u_news$ python manage.py makemigrations
(news_venv) u_news$ python manage.py migrate
~~~

## Static 데이터 모으기

```static/``` 디렉토리를 ```STATIC_ROOT``` 파라미터로 설정 후  
Django가 정적 데이터를 모으도록 명령어를 실행시켜 줍니다.   

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv/news
(news_venv) u_news$ echo "STATIC_ROOT = os.path.join(BASE_DIR, 'static/')" >> news/settings.py
(news_venv) u_news$ python manage.py collectstatic
~~~

WSGI를 통해서 서비스를 하게 될 때는 필수적으로 진행해주셔야 합니다.  
WSGI를 통하지 않고 Django runserver를 이용해서 테스트 하는 단계라면 생략하셔도 진행가능합니다.  

### 확인작업

~~~terminal
(news_venv) u_news$ tree /engn001/news_venv/news/static
~~~

## Runserver

DJango의 runserver 기능을 이용해서 서버를 띄웁니다.  
다만, DJango runserver는 단순히 테스트를 위한 기능입니다.  
운영환경에서는 미들웨어와 연동해서 서버를 띄워야합니다.  

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv/news
(news_venv) u_news$ python manage.py runserver 0.0.0.0:8000
~~~

```http://localhost:8000``` 에 접속해보면 서버가 띄워진 것을 확인할 수 있습니다.  

~~~terminal
$ curl -i http://localhost:8000
~~~

브라우저로 접속하면 REST API 테스트를 해볼 수 있는 페이지가 출력됩니다.  

#### References

  * REST API https://dev.to/enether/managing-restful-urls-in-django-rest-framework


# WSGI (Web Server Gateway Interface) 란?

Web서버가 받은 호출을 Python 어플리케이션에게 전달하고 응답받기 위한 호출조약([Calling Convention](https://en.wikipedia.org/wiki/Calling_convention)) 입니다. 

## WSGI 미들웨어란?

WSGI 호출조약의 구현체라고 볼 수 있습니다.  

![WSGI Middleware](https://lh3.googleusercontent.com/HsVbgKmzo36XJWxW-m33OQinSAwKfR1UyG1STM16WGPgtzhIyj7XkPNlvPqz8x7oYubWrclDH7bHsDU8Q84f1cnG1hoxgK2UyPGrqW4nyxfdF3M_daKpDwuEJoE4l8Stv35rA9MEBQ1Qq8V4GLtzkYQyQIiKkEWJI4mHds4_IsrFMhh2ohd1Dwpa6SWcwr9JthOu9cIK9MbWvI6HjohZUmEGkAUrUqNYG2PbtIbLpMVIeunUCqo2dnLs5JVQTTHNpormp-WPbNLJjgJ0Rh1BYmP6Uh-LAao3w9q7vSMCErQ1Hjl2ir31tZP1y2XxDRj-WE00hlDW-3NFFc15rNl8nvSLNB8x8J5Bqfi3Ks9fK7VxVoTzlbhvuT92ReuZyxSHxtIOWq9ruKEgmLbMp6xb7v0rcIDCAcxGwbIcsWXzpTj7nPtjo9zH2MQd7kxWsFgkU7Yt1YsKJanLoaoZrirfFGaCtZjIs6EGdwn9Fl0M5IoVYLK8je1kbyuVTucJ10Tknyd_9XrDzsFxaH0arJ3xZGowjlGmGgymn4k3VcOtzri6TGkrN6KpO-ORBcwlb04XreQ2s54KiLLme3ZgS6wVPkc9SBs6q9faZS8OilVDCCmmgJIsFXvn-4OH6jPftSbbeW82Ubj1zW2h3K64sta3rZqyl4-e-gly=w886-h308-no)

```Private Physical Server``` 위의 녹색 선으로 그려진 모든 것들을 합쳐서 WAS(Web Application Server) 라고 부릅니다.  
Application을 담을 Web Server 라고 생각하면 이해가 쉽습니다.  

그리고 파란색으로 칠해진 WSGI module과 WSGI Process를 합쳐서 WSGI middleware라고 부릅니다.  
하나로 구성된 것이 아니라 일부는 Nginx에 내장되어있고 일부는 프로세스로 띄워진 형태입니다.  

WSGI middleware는 Web 서버와 application을 연결시켜줍니다.  
WSGI module과 WSGI Process는 WSGI 전용 프로토콜로 정보를 주고 받습니다.  

CGI가 무엇인지 아시는 분들은 이해가 쉬울 수 있습니다.  
CGI와 비슷한데 WSGI는 Python전용이며  
CGI는 프레임워크 종류, 웹서버의 종류에 의존적이지만  
WSGI는 프레임워크와 웹서버 선택이 자유롭습니다.  

### WSGI Middleware Component 기능

  * 호출된 URL에 대한 라우팅 기능
  * 하나의 프로세스에서 다중 어플리케이션 동작하도록 처리
  * 로드밸런싱
  * 컨텐츠 전처리 ( 예 : XSLT stylesheets )
  
### WSGI Middleware 종류

  * Bjoern
  * uWSGI
  * mod_wsgi
  * CherryPy
  * Gunicorn


# WAS 구성

Nginx, Gunicorn을 이용해서 진행합니다.  

## gunicorn 설치가이드

### 설치

~~~terminal
(news_venv) u_news$ pip install gunicorn
~~~

### 설정파일 작성 

```/engn001/news_venv/news/gunicorn_cfg.py``` 파일을 생성하고 아래와 같이 설정정보를 입력합니다.
  
~~~vim
# vi /engn001/news_venv/news/gunicorn_cfg.py

daemon=True
bind='unix:/engn001/news_venv/run/gunicorn.sock news.wsgi:application'
workers=5
~~~

### 기동

~~~terminal
(news_venv) u_news$ cd /engn001/news_venv/news
(news_venv) u_news$ gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application 
~~~

### 확인작업

~~~terminal
(news_venv) u_news$ ps -ef | grep news
u_news     212     1  0 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
u_news     215   212  3 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
u_news     216   212  4 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
u_news     219   212  4 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
u_news     220   212  4 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
u_news     222   212  3 09:26 ?        00:00:00 /engn001/news_venv/bin/python3 /engn001/news_venv/bin/gunicorn -c /engn001/news_venv/news/gunicorn_cfg.py news.wsgi:application
~~~

gunicorn 프로세스가 총 6개 구동 중인 것을 확인할 수 있습니다.  
main 프로세스 하나와 5개의 worker 프로세스 개수입니다. worker 프로세스 개수는 설정파일의 worker 파라미터로 입력한 값 입니다.  


## Nginx 설치가이드

컴파일을 통한 설치가 필요시에는 [TEC > OSS > 인프라솔루션 >  Nginx > [Nginx] 설치하기](https://tec.lgcns.com/pages/viewpage.action?pageId=15469208)를 참고하시기 바랍니다.  

~~~terminal
(news_venv) u_news$ deactviate
u_news$ exit
$ yum install nginx
~~~

만약에 nginx 패키지 조회불가로 에러가 난다면 아래와 같이 진행하시면 됩니다.  
epel(Extra Packages for Enterprise Linux) yum 저장소를 추가하는 내용입니다.  

~~~terminal
$ yum install epel-release
$ yum install nginx
~~~

### 기동 확인

Nginx의 환영페이지를 출력해봅니다.  

~~~terminal
$ curl -i http://localhost
~~~

아직은 Nginx와 gunicorn이 연동이 안됐지만 결과를 비교해보기 위해서  
사용자 리스트 URI를 호출해봅니다. 404 Not Found 에러페이지가 출력될 것입니다.  

~~~terminal
$ curl -i http://localhost/news
~~~

## Nginx-gunicorn 연동가이드

### Nginx 기동중지

~~~terminal
$ nginx -s stop
~~~


### Nginx 기본설정 수정


```/etc/nginx/nginx.conf``` 파일을 백업해둡니다.  

~~~terminal
$ cd /etc/nginx
$ cp ngnix.conf nginx.conf_origin
~~~

```/etc/nginx/nginx.conf``` 파일의 server 정보를 제거합니다.  
server 정보는 38라인부터 57라인에 해당합니다. 백업해놨으니 지우시면 됩니다.  

~~~vim
# vi /etc/nginx/nginx.conf


server {
    ....
}
~~~

### Nginx server 등록

```/etc/nginx/conf.d/news.conf``` 파일을 생성하고 아래 내용을 입력합니다.  

~~~vim
# vi /etc/nginx/conf.d/news.conf

server {
        listen 80;
        server_name ${YOUR_SERVER_NAME};
        root         /usr/share/nginx/html;


        location = /favicon.ico { access_log off; log_not_found off; }

        location /static/ {
                root ${YOUR_PROJECT_DIR};
        }

        location / {
                include proxy_params;
                proxy_pass http://unix:${YOUR_RUN_DIR}/run/gunicorn.sock;
        }

        error_page 404 /404.html;
            location = /40x.html {
        }

        error_page 500 502 503 504 /50x.html;
            location = /50x.html {
        }

}
~~~

입력시 상황에 맞게 수정이 필요한 부분이 3군데 있습니다.  

 * YOUR_SERVER_NAME : 도메인주소 혹은 IP를 입력해주시면 됩니다. Nginx의 rewrite, foward 기능 사용시 주로 사용됩니다. 내부연계용 REST API 구성시에는 의미있게 쓰일 경우가 없으니 ```127.0.0.1``` 과 같은 값으로 입력하셔도 무방합니다.  

 * YOUAR_PROJECT_DIR : 프로젝트가 위치한 절대주소를 적어주시면 됩니다. 현재 진행 중인 예제의 프로젝트 절대주소는 ```/engn001/news_venv/news``` 입니다. 

 * YOUAR_RUN_DIR : Gunicorn이 bind된 run 디렉토리의 절대주소를 적어주시면 됩니다. 현재 진행 중인 예제의 프로젝트 절대주소는 ```/engn001/news_venv/run``` 입니다. 


### Nginx 기동

~~~terminal
$ nginx
~~~

### 기동 확인

~~~terminal
$ curl -i http://localhost/users
~~~

정상적으로 사용자 리스트에 대한 json이 출력됩니다.  

#### References

  * [Nginx, Gunicorn 배포 ](https://wikidocs.net/6601)
  
