---
layout: post
title: "HammerSpoon을 이용한 macOS 장비 세팅"
date: 2022-07-24 00:00:00
categories: Preferences
tags: Preferences HammerSpoon macOS setting
hidden: false
published: true
---

![HammerSpoon Logo](https://www.hammerspoon.org/images/hammerspoon.png){:class="imgTitle"}  
( 이미지 출처 : [https://www.hammerspoon.org/](https://www.hammerspoon.org/) )  


Windows , Ubuntu 환경에 익숙하던 사용자로써 MacOS 를 처음 사용하며 겪은 어려움, 찾아낸 [HammerSpoon](https://www.hammerspoon.org/)이라는 해결법 그리고 숨겨진 장점 등을 소개합니다.  
그리고 제 개인적으로 사용하는 [hammerspoon-config](https://github.com/dveamer/hammerspoon-config)를 공유하며 단축키를 설명합니다.  

<!--more-->

## 어려움

일단 Windows와 Ubuntu는 키보드 단축키가 비슷합니다. 근데 macOS를 처음 접해보니 너무나도 달랐습니다.  
예를들어, ```home```, ```end``` 키는 제가 굉장히 많이 쓰는 키인데 mac에서는 ```cmd``` + ```left/right``` 조합으로 처리해야 했습니다.  

처음에는 MacOS에서 제공하는 설정에 익숙해져보려고 노력했으나 다른 문제가 생겼습니다.  

회사의 업무는 MacOS 에서 진행하지만 게임이나 개인적인 업무는 Windows 또는 WSL 에서 진행하기 때문에 환경을 스위칭할 때마다 실수가 너무 많이 발생했습니다.  

또한 MacOS는 앱별로 단축키 차이가 큽니다.  
```home```, ```end``` 키도 IntelliJ, Visual Studio Code 같은 앱에서는 Windows와 동일하게 라인의 맨 앞/뒤로 커서가 이동합니다.    
하지만 브라우저에서 글을 쓸 때는 cursor가 페이지의 최상단, 최하단으로 이동하게 됩니다.  
앱별로 다른 단측키를 제공해서 더 편함을 느끼는 분들도 있겠지만 제 경우에는 굉장히 혼란스러웠습니다.  

## HammerSpoon의 장점 

제가 어려움을 느끼고 있을 때쯤 운좋게도 [기계인간](https://johngrib.github.io/wiki/hammerspoon-tutorial-00/)님이 HammerSpoon을 소개해주셨습니다.  

아래 인용문은 HammerSpoon이 무엇인지에 대한 설명입니다.  

> What is Hammerspoon?  
> This is a tool for powerful automation of macOS. At its core, Hammerspoon is just a bridge between the operating system and a Lua scripting engine. What gives Hammerspoon its power is a set of extensions that expose specific pieces of system functionality, to the user.  
> 출처 : [HammerSpoon](https://www.hammerspoon.org/)  

좀 더 간단하게 설명하면,  
lua 스크립트를 이용해서 MacOS에서 새로운 단축키를 추가할 수 있게 해주고 기존 키를 다른 키로 변환도 가능합니다.  

"굳이 코딩을 해야돼?" 라는 의문이 들게 됩니다. 예를 들어, [spectacle](https://www.spectacleapp.com/) 과 같은 윈도우를 자유롭게 리사이징하고 옮기는 훌륭한 앱들이 이미 있습니다.  
그럼에도 불구하고 저는 코딩을 할 수 있는 사람이라면 HammerSpoon이 가진 숨겨진 장점들이 훨씬 유용하다고 생각합니다.  

첫째로, 자유도가 높습니다. 정해진 기능이 아닌 내가 원하는 기능, 남이 만들어둔 기능을 쉽게 적용할 수 있습니다.  

둘째로, 여러개 앱 필요 없이 HammerSpoon만 있으면 세팅이 완료됩니다. 윈도우(리사이징, 모니터 옮기기), 로지텍 마우스 키 설정, 단축키로 앱 열기, 한/영전환 키, Home/End 키 등에 대한 HammerSpoon 단축키 설정을 소개 드릴 겁니다.  

마지막으로 다른 MacOS 장비로 옮겨간다고 해도 한번 작성한 [hammerspoon-config](https://github.com/dveamer/hammerspoon-config)만 적용하면 세팅이 완료 됩니다. 게다가 다른 사람들과도 세팅 공유가 굉장히 쉽습니다. 새러운 장비를 구매할 때마다 로지텍 마우스 유틸 다운받고, spectacle 다운받고, 키 변환툴 다운받고 각각 내 취향에 맞게 설정하는 작업들을 반복할 필요가 없습니다.  

<!--ads-->

## 적용방법

맥 장비에 HammerSpoon을 설치하신 뒤 [hammerspoon-config](https://github.com/dveamer/hammerspoon-config)의 ```init.lua``` 파일과 ```modules``` 디렉토리를 ```~/.hammerspoon/``` 에 위치시키시고 HammerSpoon을 껐다가 켜시면 됩니다.  
MacOS 기동시 자동으로 HammerSpoon이 켜지도록 설정하시는 것이 편합니다.  

## 단축키 설명

각자 더 편한 단축키 조합으로 변경해서 사용하시는 것을 권장 합니다.  

[Getting Started with Hammerspoon](https://www.hammerspoon.org/go/)를 많이 참고했고 aurora 기능 같은 경우는 기계인간님의 설정 코드를 참고해서 진행했습니다.  
기계인간님 HammerSpoon 소개해주신 것과 aurora 코드 모두 고맙습니다. 

### window

현재 사용 중인 앱의 윈도우의 위치와 크기, 모니터 이동을 컨트롤하는 기능을 제공합니다.  
```cmd``` + ```alt``` + ```ctrl``` 와 화살표키 조합의 단축키입니다.  

  * ```cmd``` + ```alt``` + ```ctrl``` + ```Up``` : 전체 화면 리사이징
  * ```cmd``` + ```alt``` + ```ctrl``` + ```Left``` : 가로 50%, 세로 100%로 리사이징 후 좌측 정렬
  * ```cmd``` + ```alt``` + ```ctrl``` + ```Right``` : 가로 50%, 세로 100%로 리사이징 후 우측 정렬
  * ```cmd``` + ```alt``` + ```ctrl``` + ```Down``` : 가로, 세로 80% 리사이징 후 가운데 정렬

Left 혹은 right를 두번 진행하면 다른 모니터로 이동합니다.  

### language & inputsource_aurora

우측 cmd 키를 한/영 전환키로 사용하게 해줍니다.  

```inputsource_aurora.lua``` 에 의해 한글 모드가 되면 모니터 위, 아래가 녹색이 됩니다.    

### launch

단축키로 앱을 실행시켜 줍니다. 이미 실행된 앱이라면 포커싱 시켜 줍니다. 

  * ```cmd``` + ```shift``` + ```L``` : launch mode 진입 
    - ```/``` : luanch mode에서 실행할 수 있는 단축키와 맵핑된 앱 이름을 출력
    - ```T``` : Intellij 
    - ```C``` : Chrome
    - ```S``` : Safari
    - ```F``` : FireFox
    - ```I``` : IntelliJ
    - ```L``` : Slack
    - ```V``` : Visual Studio Code
    - ```H``` : HammerSpoon
  * ```esc``` : launch mode 탈출

launch mode 인 상태에서는 모니터 위, 아래가 빨간색이 됩니다.  

### cursor

```home```, ```end``` 를 MS Windows 처럼 cursor가 위치한 라인의 맨 앞/뒤로 커서를 옮겨줍니다.  

참고로 MacOS 기본 상태에서는   
IntelliJ, Visual Studio Code 같은 앱에서는 MS Windows와 동일하게 라인의 맨 앞/뒤로 커서가 이동합니다.    
하지만 브라우저에서 글을 쓸 때는 cursor가 페이지의 최상단, 최하단으로 이동하게 됩니다.  

### mouse

Safari 브라우저에서 로지텍 마우스 3,4번 버튼을 브라우저 히스토리 뒤로가기, 앞으로가기로 변환해줍니다.  


참고로 MacOS 기본 상태에서는  
Chrome, FireFox는 별도의 설정 없이도 3, 4번 버튼으로 히스토리 뒤로가기, 앞으로 가기가 잘 되는데 Safari에서만 동작하지 않습니다.  

<!--ads-->

### refresh

모듈 수정 후 설정을 갱신할 때 사용하며 ```init.lua``` 에 작성되어있습니다.  

  * ```cmd``` + ```alt``` + ```ctrl``` + ```R``` : 설정 갱신  

## 불필요 모듈 제거 

```init.lua```을 열어서 불필요한 모듈을 주석 처리하면 됩니다.  
예를들어 language 모듈이 불필요 하다면, 


```lua
-- Hammerspoon API : http://www.hammerspoon.org/docs/index.html
-- MAC 키보드 기본 단축키 : https://support.apple.com/ko-kr/HT201236

hs.hotkey.bind({"cmd", "alt", "ctrl"}, "R", hs.reload)
hs.alert.show("Config loaded")


require("modules.window")
require("modules.mouse")
-- require("modules.language")
require('modules.inputsource_aurora')
require('modules.launch')
require('modules.cursor')
```



