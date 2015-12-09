---
layout: post
title:  "Ubuntu Terminal로 wifi 접속하기"
date:   2015-12-05 12:00:00
categories: Ubuntu
tags: Network
---

맨날 집에서 키보드를 깎다가 정말 오랜만에 분위기 전환겸 카페에 왔다.
우리 집 앞 Twosome은 커피마시면서 작업하기에 최적의 장소다.

근데 네트워크 설정이 안된다...OTL
특히 접속하려고하는 Twosome 네트워크는 Connection 버튼을 눌러도 아무 반응조차 없다.

30분정도를 Network Setting 윈도우와 씨름하다가 결국 포기하고
핸드폰을 뒤져서 Command 를 찾아봐서 해결....

<!--more-->

Terminal에서 Network-Manager를 재기동 후 재연결을 시도하면 될줄 알았으나.. 그래도 실패.. -_-

연결자체를 Terminal로 재시도했더니 연결성공..!

이제서야 맘편히 작업할 수 있겠구나

## Network-Manager
  * restart

```
  sudo service network-manager restart
```

## Network-Manager Client
  * WIFI list

```
  nmcli dev wifi list
```

  * connect WIFI 

```
  nmcli dev wifi con 'SSID' password 'wifi-password'
```
