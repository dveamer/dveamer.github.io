---
layout: post
title:  "Interface with Telegram-Cli"
date:   2015-01-01 12:00:00
categories: telegram
---

![Telegram-Cli](http://ausdroid.net/wp-content/uploads/2015/09/telegram.jpg)

<!--more-->

# Plan

## Recieve msg
 * lua script와 연동한 telemgram-cli 로 받아서 API호출하 듯이 java로 넘길 예정
   - luarocks 설치 http://www.luarocks.org/en/Installation_instructions_for_Unix
   - luasocket 설치 http://stackoverflow.com/questions/18011416/installation-of-luasocket-using-luarocks
   - socket.http 사용법 http://support.loadimpact.com/knowledgebase/articles/174637-lua-quick-start-guide

```
/* install luasocket */
luarocks install luasocket --only-server=http://luarocks.org/repositories/rocks-scm
```

``` lua
/* Way 1 to use socket */
 http.request_batch({
     {"POST", "http://test.loadimpact.com/login.php"
            , data="login=test_user&password=123"}
 })
```

``` lua
/* Way 2 to use socket */
local http = require("socket.http")
	local notiUrl = "http://localhost:xxxxx/abcd?abcd=aaa"
local result, respcode, respheaders, respstatus = http.request {
    method = "GET",
    url = notiUrl,
    --source = ltn12.source.string(reqbody),
    --[[
        headers = {
            ["content-type"] = "text/plain",
            ["content-length"] = tostring(#reqbody)
        },
    --]]
    sink = ltn12.sink.table(respbody)
}
```
  * Result
    - batch 방식은 진행실패.
    - localhost와 통신이라 get method로 진행.

## request_msg
 * java에서 OS command 실행을 통해 telegram-cli 를 매번 실행시키며 telegram command를 실행시킬 예정.
 * 메시지를 하나 보낼 때 세개 이상의 telegram command를 실행시켜야하는데 java에서 실패. python script로 작성하고 java에서 해당 script 실행하는 방식으로 진행.

## request & recieve msg
 * request_msg 용 telegram-cli르 기동,종료하는 과정이 recieve_msg 용 telegram-cli에 영향을 줘서 메시지 수신이 멈추는 현상 생김. telegram-cli의 configure 를 두개로 분리해서 진행.
   - ./tg/bin/telegram-cli -c "conf위치" -k "key위치" -s "luaScript위치" -W
 
```
//configure 파일내용
default_profile = "abcd;

abcd = {
    config_directory = ".telegram-cli/abcd";
};
```

## ETC 
 * .으로 시작하는 메시지 필터링 : .? , .기능 , .등록 등등
 * phone number, nickname(first_second) 저장
   - nickname 등록방법 : add_contact
   - telegram-cli 가 nickname 정보를 잃어버리면 갖고있는 모든 phone number에 nickname을 새로 매핑하면됨.

#### References
  - recieve_msg http://truefeel.tistory.com/224
  - telegram-cli 설치 및 기본구동 http://coffeenix.net/board_view.php?bd_code=1759
  - request_msg http://codeholic.net/post/98145891792/telegram-cli
  - telegram-cli https://github.com/vysheng/tg
  - java command 실행 http://roadrunner.tistory.com/214 
  - lua sample https://github.com/vysheng/tg/blob/master/test.lua

# 그 외
 * lua mail보내기 http://devnote.tistory.com/244
 * 행아웃으로 진행하기
   - http://clien.net/cs2/bbs/board.php?bo_table=lecture&wr_id=260006&sca=%5BPC%2F%EB%AA%A8%EB%B0%94%EC%9D%BC%5D

```
    서버에서 발생하는 특정 이벤트를 별도의 앱 설치없이 폰으로 받고 싶은 경우를 위한 간단한 팁입니다.
    - 구글톡의 행아웃연동으로 안드로이드 폰인 경우 가능한 방식인데, 아이폰은 앱을 깔아야 할거라...
    - 당연히 서버는 인터넷에 연결 가능해야 하고, sendxmpp 라는 패키지 설치가 필요합니다.
    1. 우선 알림을 전송할 구글계정을 하나 생성합니다. 비밀번호는 적당히...
    2. 생성한 구글 계정과 알림 받을 핸드폰의 구글계정을 구글톡 친구로 추가합니다.
    3. 작업을 수행할 서버 계정의 홈디렉토리에 .sendxmpprc 파일을 생성하고 아래와 같은 내용으로 1줄을 추가합니다.
     - '신규 생성한 gmail 주소';talk.google.com '신규 구글계정의 비밀번호' (ex: sendalarm@gmail.com;talk.google.com password)
    4. 크론잡이나 원하는 알립작업의 종료시에 보내고 싶은 내용을 파일로 저장하고 아래와 같은 커맨드가 실행되게 만듭니다.
     - cat '생성된 파일명' | sendxmpp -t -u 신규계정ID -o gmail.com 알림수신할ID
       (예: cat job2748_result.txt | sendxmpp -t -u sendalarm -o gmail.com receivealarm
    5. 받을 사람이 여러명이라면 구글 친구추가와 마지막 전송시 수신ID를 추가해주면 됩니다.
    프로그래머라면 xmpp library 사용해서 프로그램 안에 sendxmpp 가 수행하는 기능을 직접 구현해도 될텐데, 이건 프로그래밍 강좌가 아니니 여기까지... ^^
```