---
layout: post
title:  "Resizing Images On AWS Lambda"
date:   2018-05-27 00:00:00
lastmod: 2018-05-27 00:00:00 
categories: BackEnd
tags: AWS Python Resizing-Image
---

![AWS Lambda](https://d1.awsstatic.com/Digital%20Marketing/House/PAC/2up/PAC-Q4_House-Ads_Lambda_2up.62dc7e19b7b2e0a2c06821594c31f1ce00a6bdda.png){:class="imgTitle"}  
(이미지 출처 : [https://aws.amazon.com/ko/lambda/features/](https://aws.amazon.com/ko/lambda/features/))  

[고양이 방울(Belling The Cat)](https://play.google.com/store/apps/details?id=com.dveamer.goso) 앱을 구현하는 과정에서  
AWS S3에 업로드 된 이미지의 크기를 리사이징하는 내용을 다뤘습니다.  
(깨알같은 앱 홍보)  


AWS Lambda 위에서 구현됐고 Python PIL(Pillow) 라이브러리를 이용했습니다. 그 내용에 대해서 공유합니다.  
그리고 그 과정에서 만들어진 PIL 라이브러리를 포함한 ZIP 샘플을 공유드립니다.  

<!--more-->

## 전체구조 설명

S3에 파일이 업로드되면 그 이벤트에 의해 Lambda가 실행되고 이미지를 가로, 세로 1/2 크기로 리사이징 후 S3에 저장합니다.  

  * 업로드 된 S3 Bucket : sample
  * 업로드 된 S3 파일명 : upload/original/sample.png

  * 리사이징 된 S3 Bucket : sample-resized
  * 리사이징 된 S3 파일명 : upload/original/sample.png

## AWS Lambda에서 외부 라이브러리 사용하기

AWS Lambda에서 제공하는 기본 라이브러리 외의 다른 라이브러리를 사용하기 위해서는  
해당 라이브러리를 소스코드와 함께 ZIP 으로 압축해서 AWS Lambda에 제공해야합니다.  

하지만 이미 제가 만들어둔 결과물이 있으므로 모든 사람들이 동일한 작업을 반복해서 할 필요는 없어보입니다.  
이 글에서는 이미 만들어둔 PillowOnAWSLambda.zip 파일로 진행하겠습니다.  

하지만 추후에 Python, PIL 라이브러리 버전에 대한 이슈가 생길 경우에는 직접 AWS EC2 에서 PIL 라이브러리를 담은 ZIP 파일을 생성하셔야 합니다.  
그 내용과 과정에 대해서는 [Deploying Python Zip To AWS Lambda - 라이브러리 설치](/backend/AwsLambdaUsingPythonZip) 글을 참고하시기 바랍니다.  

### PillowOnAWSLambda.zip 다운로드 

~~~terminal
$ cd /workspace/s3_image_resizing
$ wget https://github.com/dveamer/downloads/raw/master/AWS/PillowOnAWSLambda.zip
~~~

  * 공유 위치 : [PillowOnAWSLambda.zip](https://github.com/dveamer/downloads/blob/master/AWS/PillowOnAWSLambda.zip)
  * Python 버전 : 3.x
  * Pillow 버전 : 5.0.0

Python, PIL 버전이 확실하지 않습니다.  
2018년 3월에 작업 후 EC2 인스턴스를 삭제해버리는 바람에 지금은 확인이 어렵습니다.  
나중에 다시 만들게 되면 그 때는 버전을 기록확인해서 기록하도록 하겠습니다.  

### 소스코드 작성

~~~terminal
$ cd /workspace/s3_image_resizing
$ vi aws_lambda.py
~~~

```aws_labmda.py```라는 파일을 생성하고 아래의 샘플 소스코드를 입력합니다.  

~~~python
from __future__ import print_function
import boto3
import os
import sys
import uuid
from PIL import Image
import PIL.Image
     
s3_client = boto3.client('s3')
     
def resize_image(image_path, resized_path):
    with Image.open(image_path) as image:
        image.thumbnail(tuple(x / 2 for x in image.size))
        image.save(resized_path)
     
def handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key'] 
        download_path = '/tmp/{}{}'.format(uuid.uuid4(), key)
        upload_path = '/tmp/resized-{}'.format(key)
        
        s3_client.download_file(bucket, key, download_path)
        resize_image(download_path, upload_path)
        s3_client.upload_file(upload_path, '{}-resized'.format(bucket), key)
~~~


### 소스코드를 PIL ZIP에 추가하기

~~~terminal
$ cd /workspace/s3_image_resizing
$ cp PillowOnAWSLambda.zip s3_image_resizing.zip
$ zip -g s3_image_resizing.zip ./aws_lambda.py
~~~

## ZIP 파일 Lambda 올리기 

[Deploying Python Zip To AWS Lambda - Lambda에 등록](/backend/AwsLambdaUsingPythonZip) 글을 참고하시기 바랍니다.  

## S3 Trigger 설정

AWS Lambda 웹콘솔에서 진행하시면 됩니다. (S3 웹콘솔에서 하는 법은 찾지 못했습니다.)  


![S3 Trigger 설정](https://lh3.googleusercontent.com/bXgYWT-V-6o1RXOwLbp6ZrterDkg_KZOirzNgtRxIQkeqh6xIKziwh2GOpKzV6UEN7Od7AXBE6X5BwteRdouzDOUd12Ecjj8qoH0OfIAcz6O6nCdTZ5i497ly83kYMxbY6OaLaHw_qy3HNEud0CBY92Jst31lbS15Njmj77qztdcT96jD943JLzXqF0thDIT1Lr-3xVkb8b5wjp7J6DsngLtsBeHZPLrCSOKf-PLIDQJjvKYNun6wQK7GguQelicdN9pq5VqKZ3AWXZwD9w5-FJDSzdzXRBOS_StsAPb6kjW2EJag0kPw-WHHnUKM2InhvVsVMu2rr4JXaEQGW1SDL4Z7621z-pvTKUd7kb9DU2e1iPXTYnznOJQgXYO5LbBpC1JzQYSFF118mNT0-1IIcxG_2Ch1Wocg4V52h4LvHBw46G182rOUXNM439i__0I5CAHs084BaMMrvIL5tSnkMGqgFGm26J0VvTCT_J-HD7BjW3XubXWd0THY8ApW8JQebA50SXLiJ8FAbMXLMibxVeaIiCyQZ37__ofjH9xriFInUm5yU2Q0f6DrF-N6EkTWUD1rHtA84BVJs6MHzt8248g-qVXhxNorAf6w2wi2jogGao1KFaLyhitFzyws5-PJ9YC16AqQ8qNO6S2oVU4WlonWfMrQv26=w1595-h893-no)  


  * Lambda > Function > Configuration > Designer 에서 S3를 선택합니다.  
  * 출력된 ```Configure Triggers```라는 입력창을 채워 넣으면 됩니다.  
    - Bucket : S3 Bucket 명을 고르면 됩니다. S3에서 미리 생성 해두셔야합니다.  
    - Event Type : Trigger가 반응할 이벤트 종류를 의미합니다. 파일 생성(POST), 갱신(PUT), 복사(COPY), 삭제(DELETE) 등의 이벤트 종류가 있습니다.  
    - Prefix : Trigger가 반응할 디렉토리+파일 패턴의 prefix를 적어주시면 됩니다. 디렉토리 명을 적으시면 해당 디렉토리의 파일들에 대해서 Trigger가 동작한다고 생각하시면 됩니다. 주의할점은 시작을 "/" 로 하면 안됩니다. 예를들어, ```/upload/original``` 로 설정하면 S3에 파일을 올려도 trigger가 동작하지 않습니다. 반드시 ```upload/original``` 로 설정해야됩니다.  
    - Filtter Pattern : Trigger에 등록할 파일의 확장자를 적어주시면 됩니다. 필터링 되는 내용이기 때문에 적지 않으셔도 됩니다.  
   * ```Add``` 버튼을 누른 후 ```Save``` 버튼을 눌러줍니다.  

##  Lambda의 S3 접근 권한 등록 

AWS IAM 웹콘솔에서 Lambda에게 S3 Trigger 이벤트에 대한 접근권한을 제공해줘야 합니다.  
해당내용은 AWS IAM에 대한 내용이고 이미지 리사이징에 핵심 내용이 아니므로 이번 글에서는 다루지 않겠습니다.  

