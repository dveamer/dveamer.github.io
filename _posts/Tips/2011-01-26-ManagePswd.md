---
layout: post
title:  "패스워드 관리 팁"
date:   2018-01-26 00:00:00
categories: Tips
tags: Tips Passphrase
---

![PSWD](https://upload.wikimedia.org/wikipedia/commons/thumb/4/47/Username_and_password_20170626.jpg/1199px-Username_and_password_20170626.jpg){:class="imgTitle"}  
( 이미지 출처 : [https://commons.wikimedia.org](https://commons.wikimedia.org/wiki/User:Zunter/Pictures) )  

기억해야하는 패스워드가 너무 많습니다.  

사이트마다 요구하는 패스워드 양식이 다르고  
3개월마다 새로운 패스워드로 바꿔달라고 하고  
매번 "패스워드 찾기" 를 하고있는 현실입니다.  

근데 종이에 기록하자니 잃어버릴까봐 두렵고  
컴퓨터 혹은 클라우드 디스크에 기록을 해놓자니 해킹을 당할까봐 두려우시죠?  

패스워드 관리에 대한 팁을 공유합니다.  

<!--more-->

거창한 것은 아니고 많은 분들이 이미 사용하고 계신 방법일 것입니다.  


# 요약설명

클라우드 디스크와 같이 언제, 어디서나 접속 가능한 곳에 보관합니다.  

하지만 해킹이 두려우니  
패스워드를 변환시켜서 저장하고 필요시 복원해서 사용합니다.  

근데 귀찮겠죠?  
그렇다면 이것을 어떻게하면 덜 귀찮고 빠르게 할 수 있는가를 고민해보겠습니다.  

# 변환

## 조합형 패스워드 변환작업

패스워드를 공통부분과 개별부분으로 나누고  
공통부분은 외우고 개별부분은 기록하는 방법입니다.  

예를들어 설명하겠습니다.  
A 사이트와 B 사이트의 패스워드를 기록한다고 하겠습니다.  
근데 사이트마다 패스워드 입력규칙이 다르죠.  

  * A 사이트 : 숫자포함, 대소문자 모두 포함, 특수문자 포함, 8 글자 이상
  * B 사이트 : 숫자포함, 대소문자 모두 포함, 10 글자 이상

만약 정하신 패스워드가 ```goodpassword@P``` , ```H2goodpasswordm``` 이라면  
아래와 같이 기록해두면 됩니다.  

~~~bash

# A사이트
URL : a-site.com
ID : dveamer
PSWD : {pass01}@P

# B사이트
URL : b-sitem.com
ID : dveamer
PSWD : H2{pass01}m

~~~

{pass01} 은 패스워드의 공통부분입니다.  

다시 말씀드리면, 
패스워드를 만들 때 사이트마다 패스워드가 다르더라도  
공통부분을 가지도록 패스워드를 만듭니다.  
그리고 공통부분을 제외한 개별부분만 기록합니다.  
가입한 사이트가 100개여도 1개의 패스워드 공통부분만 외우면 됩니다.  

특수문자 입력이 안되는 사이트도 있으니 공통부분에는 특수문자를 포함시키지 마세요.  
공통부분은 길수록 좋습니다. 12글자 이상을 권장합니다.  

### 부가적인 팁

{pass01} 의 01이라는 숫자를 둔 이유가 있습니다.  
예를들어, 직장인의 경우 간혹 휴가, 출장 중에 다른 동료에게 패스워드를 알려줘야할 때가 있습니다.  
회사에서 쓰는 패스워드의 공통부분을 {pass02}로 따로 정해둔다면  
동료에게 알려준다고해도 개인적인 패스워드에는 영향이 없겠죠.  

### 주의사항 

```goodpassword``` 는 예시일 뿐입니다. ```password``` 라는 단어는 패스워드로 사용하시면 절대 안됩니다.  
```password```라는 단어는 해커들이 돌리는 프로그램이 가장 먼저 넣어보는 단어이기 때문에  
```goodpassword```와  ```good1```는 해킹되는데 걸리는 시간이 거의 비슷하다고 보시면 됩니다.  

<!--ads-->

## 일반 문자열 변환작업

이번 변환작업은 암호화 작업입니다.  

Google OTP 재발급 토큰과 같은  
보관해야하지만 외울 수 없는 긴 문자열에 대해서 사용하시면 됩니다.  

외워야하는 문자열이 100개일지라도 1개의 암호화키만 외우면 됩니다.  
암호화키는 16글자 이상으로 제한합니다. 숫자, 대소문자, 특수문자 포함해도 됩니다.  

### 암호화 과정

  * 일단 기억할 1개의 암호화키를 생각합니다. ( 예 : ```암호화키는 길수록 좋습니다``` )  

  * [https://dveamer.github.io/encrypt.html](https://dveamer.github.io/encrypt.html) 에 접속합니다.  

  * ```cu2ckr7ex778d3l4``` 와 같은 OTP 재발급 토큰 값을 입력합니다. 

  * 암호화키(```암호화키는 길수록 좋습니다```)를 입력, 재입력 합니다. 재입력은 오타방지를 위해서입니다.  
 
  * ```암호화``` 버튼을 클릭합니다.

  * 암호화 된 문자열(```U2FsdGVkX1+k4baEw3OodvGSGfU55zX+ImYE8HlZ3DJPTfdrHAP7TY3HaUlHby72```)를 복사해서 다른 곳에 기록하면 됩니다.  

  * 그리고 암호화키(```암호화키는 길수록 좋습니다```)는 외웁니다. 혹은 다른 곳에 기록해서 보관합니다.  

### 복호화 과정 

  * [https://dveamer.github.io/encrypt.html](https://dveamer.github.io/encrypt.html) 에 접속합니다.  

  * 보관하고 계시던 암호화 된 문자열(```U2FsdGVkX1+k4baEw3OodvGSGfU55zX+ImYE8HlZ3DJPTfdrHAP7TY3HaUlHby72```)를 입력합니다.  

  * 기억하고 계시던 암호화키(```암호화키는 길수록 좋습니다```)를 입력합니다.  
  
  * ```복호화``` 버튼을클릭합니다.  

  * 복호화 된 문자열(```cu2ckr7ex778d3l4```)를 복사하셔서 사용하시면 됩니다.  


## 아주 긴 문자열 / 파일 변환작업

혹시 보관해야할 대상이 아주 긴 문자열이라면 일단 텍스트 파일로 저장합니다.  
그러면 보관할 대상이 파일이 되었습니다.  

해당 파일을 압축하되 암호를 걸어서 압축하시고 클라우드 디스크에 보관하시면 됩니다.  
사용하실 때는 압축을 푸시면 됩니다.  

물론 암호는 꼭 기억해두시고요.  

비트코인, 이더리움 같은 암호화화폐의 keystore 파일 보관할 때 유용하겠죠?  

### 주의사항 

압축프로그램 사용시에 암호화키는 반드시 16글자 이상을 사용하세요.  

해커가 1초에 3,000,000번의 시도를 할 수 있다고 치면,  
소문자로만 이뤄진 8글자의 암호화키는 1일내에 풀리게 됩니다.  

12글자면 1022년이 걸립니다.  
물론 해커가 사용하는 장비를 1000배로 늘리면 1년만에 풀리게 되겠죠.  
물론 개인 1명의 패스워드를 풀기 위해 그렇게까진 하진 않겠지만  
앞으로 기술이 더 발전하면서 가능성이 생길 수도 있습니다.  

16글자면 4,700,000,000년 걸립니다.  
근데 한번 더 생각해봅시다. 이왕 길게가는거 20글자 이상은 어떤가요?  
단어가 아닌 문장을 이용한다면 외우기도 쉽고 사실 20글자는 굉장히 짧습니다.  
```암호화키는 길수록 좋습니다```는 외우기 쉽지만 33글자(```dkaghghkzlsms rlftnfhr whgtmqslek```)입니다.  

20글자는 5,600,000,000,000,000년 걸리게 됩니다.  
양자컴퓨터가 이용되기 전까지는 해커가 돈을 쏟아부어도 풀 방법이 없어보입니다.  


<!--ads-->

# 보관

지금까지 변환과정을 설명드렸고  
그러면 어디에다가 기록하는 것이 좋을지를 고민해보겠습니다.  

결론부터 말씀드리자면 저는 [Google Docs](https://docs.google.com)를 이용하고 있습니다.  
본인 판단하에 다른 앱 혹은 웹이 더 사용하기 편하다면 그 것을 상용하시면 됩니다.  

[Google Docs](https://docs.google.com)를 선택한 저의 기준을 아래에 남겨보겠습니다.  

## 보관 어플리케이션 선택 기준

변환된 패스워드를 보관할 것이기 때문에 적절한 수준의 보안레벨이면 충분합니다.  

그 대신, 모든 것을 기록에 의존하게 되므로  
기록된 문서에 대한 확실한 보관 기능과  
핸드폰을 잃어려도 접근할 수 있도록 다양한 접근방법이 있어야 합니다.  

  * 보안  
    * ID, PSWD 수준의 보안  
    * OTP 사용하면 안됨  
  
  * 다양한 접근채널  
    * 핸드폰, 노트북 모두 접근 가능   
 
  * 확실한 백업  
    * 서버가 불타도 복원되야함  

  * 변경내역 관리  
    * 실수로 문서가 훼손됐을 때 과거내용을 확인하기 위함  
    * 최근 5회 정도의 변경내역만 보관되면 됨  

  * 비용  


### 고려됐던 앱 리스트

처음에는 [1Password](https://1password.com), [Lastpass](https://lastpass.com) 같은 앱을 고려했습니다.  
이 앱들은 약간의 비용이 발생하는 대신 굉장히 매력적인 기능들을 제공합니다.  
근데 변환되지 않은 패스워드를 저장해야지만 사용할 수 있는 기능들이기 때문에 제게는 무용지물의 기능들입니다.  
매년 혹은 매월 비용을 내면서 단순히 보관용도로만 사용하기에는 부담이 됩니다.  

그 다음에 가장 먼저 고려된 것은 [Google Keep](https://keep.google.com) 입니다.  
이미 평소에 자주 사용하고 있어서 더 관심을 가졌죠. 근데 실시간 자동저장 기능 때문에 뭔가 실수하면 한순간에 다 날아갈 수가 있습니다.  
열심히 찾아봤지만 과거 저장내용을 조회하는 기능은 없더군요.  

[Google Docs](https://docs.google.com)은 [Google Keep](https://keep.google.com) 에 변경내역관리 기능이 더해졌다고 보시면 됩니다.  


