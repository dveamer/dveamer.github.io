---
layout: post
title:  "Sample Json Of AWS Events"
date:   2018-03-23 00:00:00
lastmod: 2018-03-23 00:00:00 
categories: BackEnd
cates: AWS
tags: AWS 
---

AWS를 활용하다보면 Event-Driven-Programing 방식으로 시스템 제어를 쉽게 시도할 수 있습니다.  

가장 잘 알려진 예로 이미지 썸네일 생성이 있겠죠.  
S3에 이미지가 업로드 이벤트가 발생되면 정해둔 몇몇의 Lambda들에게 event를 알립니다.  
어떤 Lambda는 썸네일(thumbnail)을 생성하고, 다른 Labmda는 DB작업을 하고, 또 다른 Lambda는 로깅을 진행합니다.  

저는 주로 S3와 DynamoDB의 이벤트 알림을 주로 활용하고 있습니다.  
그외 많은 AWS 제품에서도 이벤트 알림을 제공해줄 것입니다.  


근데 AWS 이벤트 알림을 사용하다보면 (이제 본론입니다...)  
발생한 이벤트의 내용을 json으로 받게 됩니다.  
그 json 에 대한 sample을 몇가지 모아서 기록할 예정입니다.  


<!--more-->

AWS 홈페이지에서 그 json 규격에 대한 문서가 있을만한데 저는 못찾겠더군요.  
예전에 한번 찾았던 것 같기도한데.. 게을러져서인지.. 어쨋든 지금은 못 찾겠습니다.  

그 대신 필요한 이벤트를 임의로 발생시켜보고  
전달받은 json을 로깅해서 대략적인 규격을 파악하고 개발을 진행하고 있습니다.  

근데 매번 반복 작업을 하는 것 같아서 json sample을 기록해보려고 합니다.  

# How To Log Jsons

일단 Lambda에서 어떤 방법으로 로깅을 했는지 알려드립니다.  
사용한 language는 Python3.6 입니다.  

~~~python

import json

import logging
logger = logging.getLogger()
logger.setLevel(logging.INFO)

def lambda_handler(event, context) :
    
  logger.info("event : {}".format(json.dumps(event)))

~~~

예를들어 S3 이벤트에 대한 Lambda를 작성 후 테스트를 하려면  
자신의 정확한 S3 bucket 정보가 담긴 json sample이 필요하실 것이기 때문에 로깅은 한번쯤 필요합니다.  

# S3

## S3 PUT Object

~~~json
{
    "Records": [
        {
            "eventVersion": "2.0",
            "eventSource": "aws:s3",
            "awsRegion": "ap-northeast-1",
            "eventTime": "2018-03-23T13:44:30.435Z",
            "eventName": "ObjectCreated:Put",
            "userIdentity": {
                "principalId": "AWS:ADODJMDYWAJBJ27QIESKM:name_of_evnet_receiver_like_lambda"
            },
            "requestParameters": {
                "sourceIPAddress": "111.111.111.111"
            },
            "responseElements": {
                "x-amz-request-id": "D4418A1F738543E9",
                "x-amz-id-2": "FujFIZnM73L7eNSTs3pWc4FJcOmBKFQgJuaAEhglnxMgC41pFJgcCEcM2NGKQmwKlTO/5+OdMeE="
            },
            "s3": {
                "s3SchemaVersion": "1.0",
                "configurationId": "event_name_which_you_wrote_in_s3", 
                "bucket": {
                    "name": "s3_bucket_name",
                    "ownerIdentity": {
                        "principalId": "A24O2D1DIXWWMU"
                    },
                    "arn": "arn:aws:s3:::s3_bucket_name"
                },
                "object": {
                    "key": "key_of_s3_object",
                    "size": 61662,
                    "eTag": "d275d454cffddb8e946197554715f38d",
                    "sequencer": "005AB504BX5D1W1FF2"
                }
            }
        }
    ]
}
~~~


# CloudWatch Events

~~~json
{ 
  "version": "0", 
  "id": "2de27334-3716-fy7e-5x43-dwf1a5ac5447", 
  "detail-type": "Scheduled Event", 
  "source": "aws.events", 
  "account": "141855812477", 
  "time": "2018-04-03T16:07:58Z", 
  "region": "ap-northeast-1", 
  "resources": ["arn:aws:events:ap-northeast-1:141855812477:rule/scheduled_event_name_which_you_wrote"], 
  "detail": {}
}
~~~


# Dynamo DB


## Dynamo DB Insert

boto3 모듈의 UpdateItem 으로 입력했습니다.  
실제 update가 아닌 insert가 발생한다면 eventName이 Insert로 내려오네요.  

~~~json

{
    "Records": [
        {
            "eventID": "ccdc9732e4ad93fc6d1283d850567b66",
            "eventName": "INSERT",
            "eventVersion": "1.1",
            "eventSource": "aws:dynamodb",
            "awsRegion": "ap-northeast-1",
            "dynamodb": {
                "ApproximateCreationDateTime": 1523182120,
                "Keys": {
                    "key1": {
                        "S": "value"
                    },
                    "key2": {
                        "S": "value"
                    }
                },
                "NewImage": {
                    "key1": {
                        "S": "value"
                    },
                    "key2": {
                        "S": "value"
                    },
                    "column1": {
                        "S": "column1 value"
                    },
                    "column12": {
                        "N": "column2 value"
                    }
                },
                "SequenceNumber": "159997200000000018442001308",
                "SizeBytes": 200,
                "StreamViewType": "NEW_AND_OLD_IMAGES"
            },
            "eventSourceARN": "arn:aws:dynamodb:ap-northeast-1:141855812477:table/your_table_name/stream/2018-03-21T14:31:59.473"
        }
    ]
}

~~~


## Dynamo DB Delete

boto3 모듈의 DeleteItem 으로 입력했습니다. eventName은 REMOVE 이네요.  

~~~json

{
    "Records": [
        {
            "eventID": "ccdc9732e4ad93fc6d1283d850567b66",
            "eventName": "REMOVE",
            "eventVersion": "1.1",
            "eventSource": "aws:dynamodb",
            "awsRegion": "ap-northeast-1",
            "dynamodb": {
                "ApproximateCreationDateTime": 1523182120,
                "Keys": {
                    "key1": {
                        "S": "value"
                    },
                    "key2": {
                        "S": "value"
                    }
                },
                "OldImage": {
                    "key1": {
                        "S": "value"
                    },
                    "key2": {
                        "S": "value"
                    },
                    "column1": {
                        "S": "column1 value"
                    },
                    "column12": {
                        "N": "column2 value"
                    }
                },
                "SequenceNumber": "159997200000000018442001308",
                "SizeBytes": 200,
                "StreamViewType": "NEW_AND_OLD_IMAGES"
            },
            "eventSourceARN": "arn:aws:dynamodb:ap-northeast-1:141855812477:table/your_table_name/stream/2018-03-21T14:31:59.473"
        }
    ]
}

~~~

