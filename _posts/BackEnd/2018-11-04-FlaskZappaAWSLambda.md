---
layout: post
title: "Zappa를 이용해 AWS Lambda에 Flask 올리기"
date: 2018-11-04 00:00:00
categories: BackEnd
tags: BackEnd Python Flask AWS
---

![Flask](http://flask.pocoo.org/static/logo.png){:class="imgTitle"} ![AWS Lambda](https://d1.awsstatic.com/Digital%20Marketing/House/PAC/2up/PAC-Q4_House-Ads_Lambda_2up.62dc7e19b7b2e0a2c06821594c31f1ce00a6bdda.png){:class="imgTitle"}    
( 이미지 출처 : [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language)), [https://aws.amazon.com/ko/lambda/features/](https://aws.amazon.com/ko/lambda/features/) )  

Zappa라는 툴을 공유합니다.  

AWS Lambda 위에서 Flask 프레임워크를 사용할 수 있게 해주며 귀찮던 AWS APIGateway path 추가 작업이 필요 없어집니다.  

게다가 복잡한 배포 스크립트 작업 없이도 명령어 한줄만으로 배포 가능해집니다.  

<!--more-->

[How to create a serverless service in 15 minutes](https://medium.freecodecamp.org/how-to-create-a-serverless-service-in-15-minutes-b63af8c892e5) 글을 통해 Zappa에 대해서 처음 알게 됐습니다.  
글을 읽어보시면 AWS 에서 설정해야하는 부분이 꽤 많습니다.  

이번 글에서는 Flask와 Zappa에 대해서만 다룰 예정이기 때문에 AWS 설정에 관래서는 위의 글을 참고해주시기 바랍니다.  

# Flask

## Virtual Envirment

~~~teminal
$ mkdir cleannews && cd cleannews
$ python3 -m venv .env
$ source .env/bin/activate
~~~

## Install Framework & Library

Flask-API 프레임워크와 테스트를 위해 필요한 boto3(파이썬용 AWS client)를 설치합니다.  
그리고 인증처리를 위해 JWT(Json Web Token)을 이용할 예정이기 때문에 Flask-JWT도 함께 설치했습니다.  

~~~teminal
(.env)$ pip install Flask-API flask-jwt-extended boto3
~~~

기존에 Python과 AWS Lambda만으로 개발 할 때는 JWT 라이브러리를 설치해서 직접 코딩했지만  
이제는 Flask 프레임워크 위에서 돌릴 수 있기 때문에 Flask-JWT과 같은 유용한 기능을 사용할 수 있습니다.  

## 코드 작성

### controller.py

~~~python
import traceback
import os 

from flask import request, url_for, jsonify
from flask_api import FlaskAPI, status, exceptions
from flask_jwt_extended import ( JWTManager, jwt_required, create_access_token, create_refresh_token, get_jwt_identity, jwt_refresh_token_required )

from functools import wraps

import cn_exception
import cn_service

app = FlaskAPI(__name__)

app.config['JWT_SECRET_KEY'] = os.environ['cn_jwt_secret_key']

app.config['JWT_ACCESS_TOKEN_EXPIRES'] = datetime.timedelta(days=10)
app.config['JWT_REFRESH_TOKEN_EXPIRES'] = datetime.timedelta(days=15)

jwt = JWTManager(app)

def error_decorator(f):
    @wraps(f)
    def decorated_function(*args, **kwargs):
        try :
            return f(*args, **kwargs)

        except cn_exception.BizError as be :
            return jsonify({'code':be.code, 'msg':be.msg}), be.httpcode

        except Exception as ex :
            traceback.print_exc()
            return jsonify({'code':500, 'msg':'Internal Error'}), status.HTTP_500_INTERNAL_SERVER_ERROR

    return decorated_function

@app.route('/', methods=['GET'])
def ping():
    return jsonify({'code':200, 'msg':'success'}), status.HTTP_200_OK


@app.route('/blocks/<last>', methods=['GET'])
@jwt_required
@error_decorator
def get_blocked_sites(last):

    # TODO cache 필요

    account = extract_account(request)

    result = cn_service.get_blocked_sites(account, last)

    return jsonify({'code':200, 'msg':'SUCCESS', 'clean_news':result['clean_news'], 'last':result['last'] }), status.HTTP_200_OK

def extract_account(payload) :
    return get_jwt_identity().lower()

~~~

여러개의 API를 작성했지만 ```/```, ```/blocks/<last>``` 라는 두개의 API를 예시로 보여드립니다.  

AWS Lambda의 function 호출을 염두해두지 않고 REST API 작성이 가능합니다.  
controller, service 레이어로 나눠서 ```cotroller.py```서는 front-end와의 연동내용만 관리하고 ```cn_service.py```에서는 실제 업무로직만을 처리했습니다.  

기존 AWS Lambda function 호출 방식으로 개발해도 레이어를 나누는 작업은 가능합니다.  
하지만, service의 특정 메소드를 수정하게 되었는데 여러 AWS Lambda에서 사용하고 있다면 모두 다 테스트 해야하고 재배포를 해야합니다.  
근데 로컬에서 테스트가 어렵고 AWS에 테스트용 APIGateway와 Lambda를 따로 관리하는 것도 꽤나 손이 많이 가는 작업입니다.  
그래서 재활용은 포기하고 function 별로 업무로직은 분리시키는 방법을 취했었습니다. Serverless의 장점을 취하는 대신 소스코드의 품질을 떨어뜨리는 선택을 할 수 밖에 없었던 거죠.  

하지만 Flask를 사용할 수 있게 되어 로컬 테스트가 용이해졌고 이제 다시 코드품질이 앞으로 나아갈 수 있게 됐습니다.  


### run.py

~~~python
from controller import app

if __name__ == '__main__':
  app.run(debug=True, host='127.0.0.1', port=8000)
~~~

로컬 테스트를 위한 ```run.py```를 작성합니다.  

아래의 명령어로 실행시키면 테스트용 서버가 기동되며 ```127.0.0.1:8000```으로 테스트 가능합니다.  
AWS Lambda에서는 ```run.py```를 사용하진 않습니다.  

~~~terminal
(.env)$ python run.py
~~~

# Zappa

## Zappa 설치

~~~teminal
(.env)$ pip install zappa
~~~

## Zappa 배포 설정

아래 명령어로 배포환경 설정을 위한 초기화 작업을 시작합니다.  

~~~teminal
(.env)$ zappa init
~~~

배포할 s3_bucket 이름, app_function 이름, aws_region 선택 등의 어떻게 배포를 진행할 것인지를 묻습니다.  
적당히 답을 적으면 최종적으로 아래와 같은 내용을 담은 ```zappa_settings.json``` 파일을 생성합니다.  

~~~json
{
    "production": {
        "app_function": "controller.app",
        "aws_region": "ap-northeast-1",
        "profile_name": "default",
        "project_name": "cleannews",
        "runtime": "python3.6",
        "s3_bucket": "zappa-cleannews"
    }
}
~~~

주의하실 사항은 app_function과 aws_region 입니다.  

저의 경우에는 ```controller.py``` 파일에 ```app = Flask(__name__)```와 같이 Flask를 객체를 정의했습니다.  
이런 경우에는 app_function 에다가 ```controller.app```이라고 세팅해주시면 됩니다.  

aws_region의 경우에는 사용가능한 모든 region에 배포할 것인지 묻는 과정이 있습니다.  
저의 경우에는 모든 region에 배포하지 않겠다라고 설정한 것입니다.  

만약에 ```zappa_settings.json```가 원치 않는 방향으로 설정되었다면,  
직접 수정하셔도 되고 지우시고 다시 ```zappa init```을 실행하셔도 됩니다.  

## Zappa 배포 

최초 배포 때는 아래와 같이 deploy합니다.  

~~~terminal
(.env)$ zappa deploy production
~~~

AWS Console 화면으로 접속해서 AWS Lambda와 AWS API Gateway를 가보시면 자동으로 생성된 건들이 있을 겁니다. 저의 경우에는 ```cleannews-production``` 이라는 명칭으로 생성됐습니다.  

두번째 부터는 update로 배포를 진행합니다.  

~~~terminal
(.env)$ zappa update production
~~~

혹시 최초 배포과 정에서 권한을 잘못 넣었거나 다른 문제가 생겨서 실패를 했는데 그 다음부터 deploy도 안되고 update도 안된다면 일단 문제를 해결하시고 자동으로 생성된 Lambda와 API 건들을 제거 후에 다시 배포해보시기 바랍니다.  


## 테스트 

배포가 정상적으로 완료되고 나면 마지막 라인에 아래와 같은 메시지가 있습니다.  

~~~
Your updated Zappa deployment is live!: https://abcdefghi.execute-api.ap-northeast-1.amazonaws.com/production
~~~

아래와 같이 작성하신 your_test_uri를 덧붙여서 테스트 해보시면 됩니다.  

~~~terminal
$ curl -i -X GET https://abcdefghi.execute-api.ap-northeast-1.amazonaws.com/production/your_test_uri
~~~

### 502 Bad Gateway

배포를 하는 과정에서 아래와 같은 502 Bad Gateway라는 주의(Warning) 메시지가 발생할 수 있습니다.  

~~~terminal 
Error: Warning! Status check on the deployed lambda failed. A GET request to '/' yielded a 502 response code.
~~~

```GET``` 그리고 ```/``` path의 API를 하나 생성해주시면 warning 메시지는 사라지고 성공메시지가 출력됩니다.  
Zappa에서 배포 후에 해당 URI로 테스트를 진행하고 성공여부를 알려주는 것입니다.  


## 장단점

기본 세팅으로는 실행권한이 너무 방대하고 메모리는 512MB, 실행 제한시간은 30초 입니다.  

메모리, 실행 제한시간은 현재는 console 화면에서 수정해주고 있으나 매번 바꿔주기 힘드니 Zappa 설정을 통해 변경하는 법을 찾아봐야겠습니다.  
실행권한은 미리 만들어두고 Zappa 설정을 통해 선택하는 것이 가장 쉬워보입니다.  

OS 환경변수 console 화면에서 1회만 변경하면 되지만 Zappa 설정을 통해 설정하는 법을 찾아봐야겠습니다.  

위의 단점은 해결방법이 존재할 것으로 예상되며  
장점은 너무나도 분명하여 다음 프로젝트에서도 저는 Zappa를 이용한 배포를 진행할 생각입니다.  

첫번째, 소스코드 배포가 명령어 한줄로 끝나기 때문에 너무 쉽습니다.  
두번째, 소스코드의 확장이 용이 합니다. 1개의 AWS Lambda와 1개의 API Gateway path로 다양한 REST API URI를 구성할 수 있습니다. 기존에는 기능이 추가되면 AWS Lambda, API Gateway path도 추가해야했기 때문에 부수적인 작업이 많았습니다.  
세번째, Flask REST API 프레임워크를 사용 가능합니다. 프레임워크의 장점은 말할 필요가 없으며.. 이번에도 Flask-JWT를 이용해서 인증을 쉽게 처리했습니다.  

그 외에도 로컬 테스트가 용이하고 여러가지 장점이 합쳐져서 코드 재활용성이 늘어나는 효과를 봤습니다.  

쉽게말해서, 기존에 AWS Lambda를 쓰면서 생겼던 불편함이 모두 해소됩니다.  


#### References

  * [How to create a serverless service in 15 minutes](https://medium.freecodecamp.org/how-to-create-a-serverless-service-in-15-minutes-b63af8c892e5)

  * [Flask-API](http://www.flaskapi.org/)
  * [Flask & Decorator ](https://medium.com/@nguyenkims/python-decorator-and-flask-3954dd186cda)
  * [Flask-HTTP-STATUS-CODES](https://www.flaskapi.org/api-guide/status-codes/)
  * [Flask-Quick-Start](https://flask-docs-kr.readthedocs.io/ko/latest/quickstart.html)

  * [JWT 사용법](https://flask-jwt-extended.readthedocs.io/en/latest/basic_usage.html)
  * [JWT Refresh Token](https://flask-jwt-extended.readthedocs.io/en/latest/refresh_tokens.html)
  * [JWT 설정정보](https://flask-jwt-extended.readthedocs.io/en/latest/options.html#configuration-options)

  * [Python & Gmail](https://stackabuse.com/how-to-send-emails-with-gmail-using-python/)
  * [Python-Decorator](http://abh0518.net/tok/?p=604)
  * [Python-Error](https://docs.python.org/2/tutorial/errors.html)

