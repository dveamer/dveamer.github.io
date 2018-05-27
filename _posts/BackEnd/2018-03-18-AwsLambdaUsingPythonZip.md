---
layout: post
title:  "Deploying Python Zip To AWS Lambda"
date:   2018-05-27 00:00:00
lastmod: 2018-05-27 00:00:00 
categories: BackEnd
tags: AWS-Lambda Python
---

![AWS Lambda](https://d1.awsstatic.com/Digital%20Marketing/House/PAC/2up/PAC-Q4_House-Ads_Lambda_2up.62dc7e19b7b2e0a2c06821594c31f1ce00a6bdda.png){:class="imgTitle"} 
![Python](https://www.python.org/static/img/python-logo.png){:class="imgTitle"}  
(이미지 출처 : [https://aws.amazon.com/ko/lambda/features/](https://aws.amazon.com/ko/lambda/features/), [https://www.python.org/](https://www.python.org/static/img/python-logo.png))  

주로 Python 언어로 AWS Lambda를 유용하게 사용하고 있습니다.  

AWS Lambda에서 제공하는 몇몇 Python 기본 라이브러리만 사용해서 코딩을 한다면 신경쓰지 않아도 되지만  
기본 제공되지 않는 라이브러리를 사용하려면 작성한 Python 소스파일(.py)와 필요한 라이브러리를 Zip 파일로 묶어서 AWS Lambda에 올려야합니다.  

<!--more-->

문맥상 주의사항을 가장 하단에 위치시켰습니다.  
주의사항을 반드시 읽어보시고 진행하시기 바랍니다.  


## 작업 디렉토리 생성

~~~terminal
$ mkdir -p /workspace/s3_image_resizing
~~~

```/workspace/s3_image_resizing``` 라는 작업 디렉토리는 예시일 뿐입니다. 필요에 따라 바꾸시면 됩니다.  

## 소스코드 작성

~~~terminal
$ cd /workspace/s3_image_resizing
$ vi aws_lambda.py
~~~

```aws_labmda.py```라는 파일을 생성하고 아래와 같이 소스코드를 입력합니다.  

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

위 소스코드 내용은 S3로 PUT된 이미지 파일을 resizing하는 내용입니다.  
지금 소개드리는 주제와 전혀 상관없으니 세세한 내용은 무시하시고  
PIL 라이브러리를 import해서 사용하려고 한다는 것만 기억하시면 됩니다.  


다만, 소스코드를 실행을 시켜보면 boto3 라이브러리와 PIL 라이브러리가 없다는 에러가 발생합니다.  

## 라이브러리 설치

위의 소스코드를 PC에서 실행시키기 위해서는 ```pip3``` 을 이용해서 라이브러리 설치 후 재시행해보시면 됩니다.  

근데 AWS Lambda에는 PIL 라이브러리가 설치되어있지 않습니다.  
소스코드를 올려서 실행시킨다 하더라도 라이브러리가 없다는 에러가 발새합니다.  
AWS Lambda에서 실행시키기 위해서는 PIL 라이브러리를 소스코드와 함께 ZIP으로 묶어서 올려야 합니다.  

방법은 단순합니다. ```pip3```로 설치 시에 -t 옵션을 이용해 라이브러리 설치 위치를 지정하면 됩니다.  

[주의사항 - 작업공간](#작업 공간) 을 확인하시고 진행하시기 바랍니다.  

~~~terminal
$ cd /workspace/s3_image_resizing
$ sudo pip3 install Pillow -t .
~~~

boto3 라이브러리는 AWS Lambda에서 기본적으로 제공하기 때문에 ZIP으로 패키징할 대상에 포함되지 않습니다.  
특정버전의 boto3가 필요하시다면 패키징 하셔도 되지만 그런 사유가 아니라면 용량만 더 차지할 뿐입니다.  
참고로 AWS Lambda에 올리는 ZIP파일은 용량제한이 있습니다.  

로컬 테스트를 위해서 boto3를 설치하셔야 된다면 3가지 정도의 방법이 있을 것 같습니다.  
다만 그에 대한 장단점과 진행방법에 대한 자세한 설명은 기술하지 않을 예정입니다.  

  * -t 옵션을 주고 설치한 뒤 ZIP 과정에서 boto3를 제외시키는 방법
  * -t 옵션을 빼고 설치해서 global하게 설치
  * virtual-env 를 이용하고 그 위에서 global하게 설치


## 압축

Linux 기준이고 zip 프로그램이 설치되어있다고 가정했습니다.  
하위 디렉토리까지 전체를 압축해버리시면 됩니다.  

~~~terminal
$ cd /workspace/s3_image_resizing  
$ zip -r s3_image_resizing.zip ./*  
~~~

[주의사항-압축대상](#압축대상 ( The Directory Content, Not The Direcotry) 을 확인하시고 진행하시기 바랍니다.  


## Lambda에 등록

ZIP 파일을 Lambda에 등록하는 방법은 두가지가 있습니다.  

Lambda에 직접 업로드하는 방법과  
S3로 업로드 후, 그 파일의 S3 link 주소를 Lambda에 기입하는 방법입니다.  

Lambda에 직접 업로드 하는 방법은 10MB 이하의 파일만 가능합니다.  
그렇다고 S3를 통한 업로드 방법은 제한용량이 없는 것은 아닙니다.  

그래도 제한이 좀 더 높은 S3를 통한 업로드 방법을 가이드 드립니다.  

### S3 업로드

CLI 접근권한 설정을 해두셨다면 CLI로 업로드하시면 됩니다.  

~~~terminal
$ cd /workspace/s3_image_resizing
$ aws s3 cp ./s3_image_resizing.zip s3://sample/s3_image_resizing.zip
~~~

CLI 접근권한 설정에 대해서는 이글에서는 다루지 않을 예정입니다.  
접근권한이 필요하시면 일단 AWS S3 웹콘솔에서 upload 버튼을 이용하셔도 됩니다.  

### S3 link 주소 확인

AWS S3 웹콘솔에 접속해서 업로드한 파일의 정보를 열어봅니다.  

![s3_link_01](https://lh3.googleusercontent.com/B7lcgmMIC4eNxye6NwwS8Rc2ijsWk5J7AyYZvAujqZ_QOVj72LLgLxubVZ2VKJp2JIFgBh9cIaSAnTyuQnQdpxtWo6BnucLJ6XzZpCFU79uDduh3N-D6ViO7s4z5XWFDPp-sGYS41x4RcUAhVPGl-MaRwRE8BLR1yoOt6bK12HQTDGp3fwen3G4l8zehOUVBFVY06J1CuifLdA9Z5zaFdeWXll-PhZ7s095voDK_V8yBUHZ4ear7u-dDpThMCN71VD9KXzvoiAl8EGvo3GAmj7S4a1ctzR-Z8dVgFvD56AIEFNmX92SLScZXqmC6KczwZo_m4n3B7I1L5f3Yeo5JDZb1WqZJ80ZSqNwt5A7_NNZhzOQz6S5tYaYEeGFBUHTfvuNn0ZlQ25r8oegIW-RN-tKlT5ltmB3Skk6XwtSWrti0lxd0B00w4gJ65w_wDlNX4hatvEq9-HGZyd9_1fC0AA_JDao_sVMOiupvgKTsgKMBuYciYtDqxI_j3gSRW4Y0O_Rap5kyt5IvYM-ntkr3VhDJRJ8GtZ9lrY9h_oZW2byRDKYJPFoEbiTXX4LHheU78XKakk2xkTWo8YCh5zeR_iHtYNdSkfCYg1svosjhCJMV5gdT8xhdFI2bs_F7MjRQQCqPslsAVHvQZ5Vgh46lrTBQbDzytx4G=w1623-h475-no)  

S3 Link 정보를 복사합니다.  

![s3_link_02](https://lh3.googleusercontent.com/3nd-rx9gGpgf82hAemt7U-MbDy4i1Dd42qup7JAjzSITSP_p_B0fxJeeFmQmuFr_2apeOiLbPOK4BUiR8mJ4EvfXuSwxVlW_PIvl1Ta2swZn0VWDeyYFHaEHM4zZ6hOjieUT8gbaE90lqnaNrvfkvVPx0pSeP2dp8ZofTaE7faXM3V8PTRuwXRu4qfUmYAPBdDd3-rgzt16QgSleU8r2Z6vY-mBwhNMjRbn_r65fJrRECc2oF4NTyFXolp6CNpzK1EdYYRAPMMCFRCoHJiD0Bdrz7W1x9qrA67vuJ8FM55NWV0a73SL-4KM2rk3Qr_XoHEz2ORYKOrk1QtrqajL-AAsI_NuY4sGvGc97p-KzVNRdfIeqenP48rhBmFSlck5Azg3qY2WcnkokyqInTENif3DTmxNuvXlJeSgAtMmM98s5wjuB5Pup6w8JpNHculTfTgGSv18vlfN13WE7-yw8DvGOuISsdJwAjfAtyv8W_2KZ_72FFrxjp7IKDD1KKoixuKnm1qeAG9j54t2O5Fkof7hnhtXUuj7aKPWr8_44LCOARbOackBb_8gIncCn0WqIR545mYec55TMX3usAcLnnpktGVFamdWzftPCXFckpox470sSqx8h4qE4TzqGTglU65ttrK650GW5zsG2C0rc3i-2paXlkR3U=w1627-h684-no)  


### S3 link 주소를 Lambda에 입력 

  * ```Code entry type```을 ```Upload a file from Amazon S3```로 변경합니다.  
  * 복사해둔 S3 Link 정보를 입력합니다.  
  * Save 버튼을 누릅니다.  

![s3_link_03](https://lh3.googleusercontent.com/XH13YQS7GG0uUmMmW-0iDsd98uXOQPbZmPcOYEA2J1pQMuNfANovfuGVyFOV017qoapVteq9AbOs-3ppB8z7YRgrJ7lqc69oaoPekMYpXMF5Py0zTXLoQ1k-EeYe3OdW0WwBv5fAPNlHp7KV_7ou2dtdywNc6WNh2ThbDGlmNf1NkhzKhXRXxhGFt1GHT4CVxxKHCkPN5-nEBoATofs6sGa-RAkYZr9HYAvf4CePhUfNpF3tR-dbMawmJ0uxRdjbaVeRSA2gVAtmB34m7bTov0Yjx9SbOg4Wrzx_xumWzGLxMJakxGDxkpNVZbQF9NXS2vklk9j_UktStpIo3pVF5rsyBcATIs_iEngrblWtIU1d7UuPSFcsHiVohAUUAI6HbzTzgtxy6YxKSMcT4yoaLjCbDrLB0ySuxE0CEtSUhhzSrhdxVGxarT6sOdtOTzl9sSEj1WsMYcUw3pLTjQAErJuUQGoGNiA3SkiHyi24JWzYye6T3FZ9UrMeWltd6j-wZ1-qs8PZedsoxI-CqU2FJDJjxeFDWEig1UnjhlLLcd1kjAcK3YO3r0M6-ePvxrZNqJkHnRhpc5VkgXcBfMV7s9y6vu9AWmsvvfmDcXOzmqgjrapI55PnZsBRqlx1iXKa7RACHO7qmgcLsctsgF3i0IuTO27wjy2x=w1595-h813-no)


# 주의사항

## 압축대상 ( The Directory Content, Not The Direcotry )

> 4. Zip the content of the project-dir directory, which is your deployment package.
>   Important
>   Zip the directory content, not the directory. The contents of the Zip file are available as the current working directory of the Lambda function. For example: /project-dir/codefile.py/lib/yourlibraries
>    출처 : [AWS Developer Guide - 배포패키지 만들기 (Python)](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python-how-to-create-deployment-package.html)

압축대상은 소스파일(.py)가 위치한 디렉토리가 아니라  
소스파일(.py)과 라이브러리와 같은 디렉토리 내용물이어야 합니다.  

```/workspace/s3_image_resizing/aws_lambda.py``` 라는 파일이 있을 때  
```/workspace```에서 압축명령어를 실행하면 ```s3_image_resizing``` 디렉토리째로 압축이 됩니다.  
그렇게하지 말고 ```/workspace/s3_image_resizing``` 디렉토리 안에서 내용물을 압축하라는 얘기입니다.  

그 이유는 AWS Lambda의 handler정보 설정과 연관됩니다.  
handler정보에 입력된 ```aws_lambda.lambda_function```의 의미는  
AWS Lambda가 실행요청을 받으면 ```aws_lambda.py```라는 소스파일의 ```lambda_function```이라는 method를 실행시키겠다는 의미입니다.  

근데 최상위 위치에 ```aws_lambda.py```라는 파일이 없으니 에러가 발생하게 됩니다.  

~~~python
Unable to import module 'aws_lambda': No module named 'aws_lambda'
~~~

만약에 굳이 디렉토리를 꼭 넣고 싶다면 AWS Lambda의 handler설정에 디렉토리 정보를 함께 넣어주시면 됩니다.  
예를들어 ```s3_image_resizing/aws_lambda.lambda_function```로 hander에 정보에 입력하시면 됩니다.  

## 작업 공간

다른 라이브러리를 함께 ZIP으로 압축하는 과정을 개인 PC에서 진행하는 것보단  
AWS EC2 에서 진행하시는 것을 추천드립니다.  

이유는 AWS Lambda의 실행환경이 AWS Linux이기 때문입니다.  

제 개인 PC에서 ZIP으로 압축한 후 AWS Lambda에 올려서 실행을 했더니 아래 에러메시지가 출력됐습니다.  

~~~python
Unable to import module 'aws_lambda': cannot import name '_imaging'
~~~

에러메시지에서 원인을 파악할 수는 없었습니다.  
하지만 AWS EC2에서 라이브러리 설치, ZIP 을 진행할 경우 문제 없이 Lambda에서 실행됐습니다.  

항상 AWS EC2에서 진행해야하는 것은 아닙니다.  
이전에 다른 라이브러리를 ZIP 할 때는 문제가 없었는데 최근에 사용한 PIL 라이브러리를 ZIP 한 경우에만 문제가 생겼습니다.  
OS 혹은 다른 것에 의존성을 가진 라이브러리에 대해서만 문제가 생기는 것으로 추측됩니다.  

### PIL 패키징된 ZIP 파일

AWS EC2에서 PIL 라이브러리를 설치하고 압축하는 과정은 생각보다 번거로울 수 있습니다.  
어떤 분들은 EC2 설치방법부터 학습을 시작해야할 수도 있으니까요.  

근데 "내가 만든 것과 다른 사람이 만든 결과물은 동일할텐데.. 서로 공유하면 되는거 아닌가?" 라는 생각이 들었습니다.  
그래서 제가 만들어둔 PIL 라이브러리를 공유드립니다.  
물론 특정 기능 혹은 보안을 위한 버전별 공유는 기대하시지 마십시오.  

공유위치와 사용방법에 대해서는 [Resizing Images On AWS Lambda](/backend/ResizingImagesOnAwsLambda) 글을 참고하시기 바랍니다.  

### Setting Python On AWS EC2

~~~terminal
$ sudo yum install python36-devel python36-pip gcc libjpeg-devel zlib-devel openssl openssl-devel -y
~~~

#### References

  * [AWS Developer Guide - 배포패키지 만들기 (Python)](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python-how-to-create-deployment-package.html)
  * [http://sarc.io - 삵(sarc.io) 님 블로그](http://sarc.io/index.php/aws/805-aws-lambda-zip-python)
  * [http://hochulshin.com - hochulshin 님 블로그](http://hochulshin.com/aws-s3-lambda/)
  * [http://www.perrygeo.com - Matthew T. Perry 님 블로그](http://www.perrygeo.com/running-python-with-compiled-code-on-aws-lambda.html)
  * [AWS Developer Guide - 사용사례 > 배포패키지 생성](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/with-s3-example-deployment-pkg.html#python)
