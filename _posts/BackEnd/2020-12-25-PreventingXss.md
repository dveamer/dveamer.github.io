---
layout: POST
title: "HTML Editor과 XSS(Cross Site Scripting) - Python Bleach"
date: 2020-12-25 00:00:00
lastmod: 2020-12-25 00:00:00
categories: BackEnd
tags: BackEnd
---

HTML 에디터를 포함한 시스템을 만들게 되면 XSS(Cross Site Scripting)를 대응하기 위해 신경쓸 것이 많습니다.  
Cloud Obect storage를 사용해서 진행했던 방법을 공유하고 그 과정에서 사용했던 Bleach라는 Python 라이브러리를 소개합니다.  

<!--more-->

# REST API와 XSS

REST API 서버에 대한 XSS 대응은 어렵지 않습니다.  
POST, PUT 과 같이 데이터를 저장하기 위해 호출된 API들에 대해서 HTML tag에 사용되는 ```<```, ```>``` 와 같은 몇개의 특수기호들을 ```&lt```, ```&gt```와 같은 다른 문자로 변환해서 저장하면 됩니다.  

반대로 조회 시점에 변환해서 출력하는 방법도 있지만 데이터는 1번 저장되어 여러번 조회 되기 때문에 저장 시점에 처리하는 것이 훨씬 효과적입니다.  

# 골치거리 HTML Editor

HTML 에디터는 사용해야하는 HTML tag들이 존재하기 때문에 특수기호를 변환해서는 안되고 허용되지 않는 tag들에 대해서만 별도의 처리를 해야합니다. 게다가 tag만이 아니라 tag가 가진 attribute, style 등의 정보도 허용할 대상을 관리하고 별도 처리를 해야합니다.  

Tag, attribute, style 여부를 파악해야하므로 문자열 혹은 문자가 발견시에 치환하면 되는 기존 방법에 비해 성능적으로도 손해가 큽니다.  

그렇기 때문에 HTML 저장 API와 REST API에 대한 XSS 대응을 다르게 처리 해야합니다. 그 이야기는 HTML 저장 API 리스트 관리가 필요해집니다. 다른 방법으로는 HTML 관리 마이크로 서비스를 별도로 떼어내는 방법입니다.  

REST API 마이크로 서비스는 단순히 치환작업만 진행하고  
HTML 관리 마이크로 서비스는 보다 엄밀한 XSS 대응 작업을 진행하면 됩니다.  

## Cloud Storage 활용

최근 진행했던 프로젝트에서는 AWS S3, GCP GCS와 같은 cloud object storage를 활용하는 방법으로 진행했습니다.  

HTML 문서는 cloud object storage에 보관하고 위치와 같은 메타정보만 REST API를 통해 파일 관리 마이크로 서비스에 보관했습니다. 결국 HTML 저장 API는 존재하지 않고 REST API만 존재하기 때문에 XSS 대응이 더 쉬워졌습니다.  

REST API를 제공하는 마이크로 서비스들은 대부분 Java로 진행했기 때문에 [Luxy](https://github.com/naver/lucy-xss-filter) 오픈소스를 이용해서 진행했습니다.  

HTML에 대한 XSS 대응은 cloud object storage에 HTML이 저장된 이후에 AWS Lambda, GCP Function에서 비동기로 진행했기 때문에 Java보다는 Python이 유리하여 [Bleach](https://github.com/mozilla/bleach)를 사용했습니다.  


### Cloud Storage를 사용한 이유  

Cloud object storage를 사용한 것은 XSS 대응만을 위해서는 아니고 여러가지 이유 때문에 파일 보관 자체를 cloud object storage에서 하도록 결정했습니다.  

HTML과 관련된 예를 하나 들면,  
HTML 에디터를 사용하면서 이미지를 추가하면 이미지는 별도로 보관하고 해당 이미지의 주소를 HTML img tag에 삽입해야합니다. 여기서도 많은 고민거리들이 발생합니다.  

  * HTML에 대한 접근 제어를 한다면 이미지도 접근 제어를 할 것인가  
  * HTML과 이미지 URL의 도메인이 일치되도록 설계필요  
    - 도메인이 다르면 이미지의 URL 전체 경로를 HTML에 기입 필요  
    - 추후에 도메인 변경시 모든 HTML을 열어서 이미지 src를 수정해야하는 상황 발생  
    - 개발, 운영 환경 그리고 내부망, 외부망 환경 별로 관리 잘 해야함  

이러한 고민거리들을 없애기 위해서 이미지 추가시 base64 형태의 이미지 데이터 전체를 HTML에 삽입해서 보관하는 방법으로 진행했습니다. 그리고 해당 HTML을 약간은 무거울 수 있는 파일로 생각해서 다루었고 DB같은 저장소가 아닌 cloud object storage로 보관 장소를 정했습니다.  

# Bleach 

HTML sanitizing library로써 cleaner, linkify 등의 기능을 제공합니다.  

  * 문서 : [https://bleach.readthedocs.io](https://bleach.readthedocs.io)
  * 소스코드 : [https://github.com/mozilla/bleach](https://github.com/mozilla/bleach)
  * 라이센스 : Apache License v2

## 가상환경 생성 

~~~terminal
$ mkdir bleach-test 
$ cd bleach-test 
$ python3 -m venv .env
$ source .env/bin/activate
~~~

## Bleach 설치

~~~terminal 
$ pip install bleach
~~~

## Cleaner 사용법

허용할 tag, attribute, style, protocol 등을 정의하면 허용되지 않은 HTML 코드는 제거해줍니다.  

HTML 에디터에서 작성된 HTML 파일을 위해 사용할 것이기 때문에 HTML 에디터에서 필요로하는 tag, attribute, style, protocol 들을 정의합니다.  

~~~python

from bleach import Cleaner

ALLOW_TAGS = [ 
    'p', 'b', 'i', 'strike', 'ul', 'li', 'ol', 'br', 'button', 
    'table', 'tbody', 'td', 'tfoot', 'th', 'thead', 'tr', 'tt', 
    'span', 'blockquote', 'hr', 'a', 'img', 
    'h1', 'h2', 'h3', 'h4', 'h5', 'h6', 'hr', 'div'
]

ALLOW_ATTRS = {
    '*': ['class', 'style', 'alt'],
    'a': ['href', 'rel'],
    'img': ['height', 'width', 'style', 'src'],
}

ALLOW_STYLES = ['color', 'cursor', 'float', 'margin']

ALLOW_PROTOCOLS = ['http', 'https', 'mailto', 'data']

cleaner = Cleaner(tags=ALLOW_TAGS, attributes=ALLOW_ATTRS, styles=ALLOW_STYLES, protocols=ALLOW_PROTOCOLS)

print(cleaner.clean('<img alt="abcd" src="javascript:alert(1);">'))

# <img alt="abcd">
~~~

## Test Cases


~~~python
...(생략)

def test_valid(value) :
    clean_value = cleaner.clean(value)
    try :
        assert( value == clean_value)
    except AssertionError as ex :
        print('value : {}'.format(value))
        print('clean_value : {}'.format(clean_value))
        raise ex


def test_invalid(value, expected_value) :
    clean_value = cleaner.clean(value)
    try :
        assert( value != clean_value)
    except AssertionError as ex :
        print('value : {}'.format(value))
        print('clean_value : {}'.format(clean_value))
        raise ex

# Valid Cases
test_valid('<a href="http://dveamer.github.io">hi</a>')
test_valid('<a href="https://dveamer.github.io">hi</a>')
test_valid('<a href="mailto://dveamer@gmail.com">hi</a>')

test_valid('<img alt="abcd" src="http://dveamer.github.io">')
test_valid('<img src="https://dveamer.github.io">')
test_valid('<img src="data:text/plain;base64,SGVsbG8sIFdvcmxkIQ%3D%3D">')

# Invalid Cases
test_invalid('<a href="javas&#x09;cript:alert(1)">hi</a>', '<a>hi</a>')
test_invalid('<img src="javascript:alert(\'XSS\');">', '<img>')
test_invalid('<img src="javas&#x09;cript:alert(\'XSS\');">', '<img>')
~~~


