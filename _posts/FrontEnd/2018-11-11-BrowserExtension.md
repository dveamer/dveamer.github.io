---
layout: post
title: "확장앱(Add-On, Extention) 개발기 (Chrome, Firefox 차이 설명)"
date: 2018-11-11 00:00:00
categories: Browser-Extension
tags: Browser-Extension Chrome Firefox Add-On
---

![Chrome](https://www.google.com/chrome/static/images/chrome-logo.svg){:class="imgTitle"} ![Firefox](https://addons-amo.cdn.mozilla.net/fe725b21e516c3f4810194197c689f6c.svg){:class="imgTitle"}  
( 이미지 출처 : [google.com](https://www.google.com), [https://www.mozilla.org/](https://addons-amo.cdn.mozilla.net)  )  

[Clean News](https://cleannews.dveamer.com)라는 Chrome과 Firefox 확장앱(Add-On, Extension)을 만들면서 기록해둔 몇가지를 공유합니다.  

<!--more-->


저는 평소 OS는 Ubuntu를 쓰고 브라우저는 Firefox를 씁니다.  

브라우저 확장앱을 만드는 것을 시작했을 때 별 생각 없이 Firefox에 맞춰서 만들기를 시작했습니다.  
어자피 Firefox용을 만들고 Chrome용을 만들 생각이었기 때문에 순서는 상관없다고 생각했고  
만들어서 제가 써보면서 테스트하기 위해 Firefox로 진행을 시작했습니다.  

그러한 이유로 대부분의 기록들이 Firefox 기준으로 적혀있습니다.  

재미있는 점은 지금은 Chrome으로 먼저 개발을 하고 있습니다. 그 이유는 아래에서 설명드립니다.  


# 확장앱 개발자모드 

## Firefox 

  * [about:debugging](about:debugging) - 개발한 확장앱을 임시로 등록해서 테스트해 볼 수 있습니다.  
  * [about:addons](about:addons) - 사용 중인 정식 확장앱들을 확인할 수 있습니다.  

## Chrome

  * [chrome://extensions/](chrome://extensions/) - 정식앱과 개발 중인 앱의 리스트를 확인할 수 있습니다.  


# 확장앱 등록하기

아래 링크로 이동해서 가입 후 등록하시면 됩니다.  


## Firefox

  * [앱 등록](https://addons.mozilla.org/ko/developers)  
  * [압축하는 방법](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Package_your_extension_)  

Firefox는 등록이 매우 간단합니다. 만들어둔 zip 파일, 이미지 1장 그리고 앱의 간략한 설명만 적어주면 등록이 가능합니다.  
게다가 등록하고 10분 이내에 등록처리되었다는 메일을 받아 볼 수 있고 바로 정식 앱을 설치 가능합니다.  
아마도 앱 등록건수가 많지 않고 점거 기준도 낮게 잡혀있는 것 같습니다.  
물론 나중에 상황이 달라지면 달라질 수도 있겠죠.  

특이한 점은 동일한 zip 파일을 두번 업로드해야한다는 점입니다.  
첫번째는 자가 점검용으로하고 두번째는 실제 제출용으로 하는 것 같습니다.  
왜 그렇게 하는지는 모르겠습니다.  

### 주의사항 

```element.innerHTML = "";``` 을 사용하는 것은 검토시 반려사유가 될 수 있다고 합니다.  
저의 경우는 사용하던 innnerHTML을 [appendChild](https://www.w3schools.com/jsref/met_node_appendchild.asp) 로 교체 했습니다.  
Jquery에서도 innerHTML을 포함하고 있어서 경고 메시지는 받았지만 반려되진 않았습니다.  

## Chrome

  * [앱 등록](https://chrome.google.com/webstore/developer/dashboard)  

Firefox와 달리 필요한 준비물이 많고 구체적입니다.  

크롬은 최초 1회 5$ 비용이 발생합니다. 국제결제가 가능한 신용카드가 필요하니 준비하시기 바랍니다.  

zip파일 외에도 홍보용 이미지, 스캔 이미지 등 준비가 필요합니다. 게다가 이미지의 사이즈, 여백 사이즈가 정해져있습니다.  
확장 앱을 만드는 과정에서 확정된 이미지를 만들면 등록 과정에서의 태클로 인해 정신적 데미지를 입으실 수 있습니다.  
앱 개발 초반에 먼저 확장 앱을 등록해보면서 이미지를 만들어나가시길 추천합니다.  

앱을 올리고 나면 "2~3일 이내에 점검 완료 후 등록 예정" 이라는 메시지가 뜹니다.  
제가 만든 확장앱이 사용중인 tab의 HTML에 대한 조회 권한이 있어서 인지는 모르겠으나..  
등록하는데 걸리는 시간이 정말 3일이 걸립니다. 일요일 밤에 등록요청을 해두면 수요일 밤에 등록이 완료되는 것을 경험했습니다.  

그리고 등록 도중에 추가 변경 건이 있으면 기존 등록요청을 취소하고 새로운 버전을 등록해야합니다.  
즉, 한번 등록요청을 하면 추가 개발건이 있어도 등록이 완료 된 뒤에 재 등록요청하는 것이 가장 효율적입니다.  
최소 3일마다 등록이 가능한 형태 입니다.  


# Chrome VS Firefox

위의 등록하는 과정에서는 Firefox가 Chrome보다 개발자 친화적입니다.  
하지만 시장 규모라던가 보안 측면에서 Chrome이 Firefox보다 낫다는 설명이 되기도 합니다.  

이번에는 개발하는 과정에서의 차이를 얘기해보겠습니다.  

당연한 이야기지만 API가 다릅니다.  
```sendMessage``` 라는 API를 예로 들면, chrome은 ```chrome.extension.sendMessage({});```, Firefox는 ```browser.runtime.sendMessage({});```와 같이 사용합니다.  
대부분 Chrome은 ```chrome```으로 시작하고 Firefox는 ```browser```로 시작합니다.  

파리미터 개수가 다른 경우도 많습니다.  
Firefox는 성공, 실패 두가지 경우의 callback function을 파라미터로 받는 반면,  
Chrome은 성공에 대한 callback function만 파라미터로 받는 경우가 대부분 입니다.  

그리고 아예 기능이 차이나는 경우도 있습니다.  

Firefox는 ```webRequest```를 이용해서 intercept하고 logging만이 아니라 blocking, redirect가 가능합니다.  
하지만 Chrome은 ```webRequest``` 으로는 intercept 후 logging만 가능합니다.  

대신 chrome에서는 ```declarativeWebRequest```을 따로 제공하고 제한적인 blocking은 가능합니다.  
하지만 chrome 도 beta channel에서만 해당 기능이 가능합니다.  
저의 경우는 Ubuntu에서 Chrome Chromium 브라우저로 테스트를 했는데, 여기서는 잘 돌아가던 ```declarativeWebRequest``` 기능이  
정식 앱 등록 후에 Windows의 Chrome 브라우저에서 테스트를 했더니 ```declarativeWebRequest```는 beta channel에서만 사용 가능하다는 메시지가 출력됐습니다.  

UX 측면에서는, 파폭은 팝업 화면에서 마우스를 벗어나면 팝업이 자동으로 닫히게 되는데 크롬은 팝업을 닫는 액션을 취하지 않는한 계속 유지됩니다.  
근데 파폭은 수시로 팝업이 닫히기 때문에 팝업에서 입력한 데이터를 바로바로 보관할 임시보관소가 필요합니다.  

<br>

지금까지는 차이점을 줄줄 기술했고 정리해서 제 느낌을 적어보겠습니다.  

Chrome이 더 엄격하고 테스트하기 편합니다.  

Chrome은 실패에 대한 callback function이 없지만 에러가 발생하면 runtime 에러처리 되어 기능이 정지하고 개발자 모드에서 에러를 표시해줍니다.  
Firefox는 callback function만 실행시켜주고 나몰라라 하는 느낌입니다. 심지어 callback function 에서 에러가 발생하면 callback function의 정상 동작 여부를 파악할 방법이 없습니다. 매번 로깅을 하거나 데이터를 확인해서만 확인이 가능합니다.  

초반에는 Firefox에서 개발하고 Chrome으로 porting을 했습니다. 그러다보니 Firefox에서 잘 돌아간다고 생각했던 기능이 깔끔하게 마무리 되지 않고 있었던 것을 Chrome에서 확인하게 되는 경우가 많았습니다. 그래서 지금은 거꾸로 Chrome에서 개발하고 Firefox로 porting 중입니다.  

<br>

그리고 Firefox는 callback의 시작시점도 애매합니다.  
storage.local에 데이터 저장을 요청하고 성공 callback function에서 ```location.reload();```로 화면을 재로딩 하는 경우를 예로 들겠습니다.  
일단 Firefox는 화면이 재로딩은 되지만 데이터는 저장되어있지 않습니다. 반면 Chrome은 재로딩과 저장이 모두 성공적으로 이뤄집니다.  
제 추측으로는 Firefox의 경우는 저장이 완전히 완료된 후에 callback function이 실행되는 것이 아닌 것 같습니다.  
callback function에서 ```location.reload();```를 setTimeout을 걸어서 1초 대기 후에 처리하면 저장과 재로딩이 모두 이뤄집니다.  

근데 사실 이것은 Firefox의 잘못은 아닌 것 같습니다.  
처음에 확장앱의 구조 자체를 제가 잘못 이해하고 진행한 것은 아닌가.. 라는 생각 중입니다.  

화면의 lifecycle 내에서 storage를 접근하는 것은 피하고 화면의 lifecycle과 무관한 background.js에서 storage를 접근하는 것이 맞는 것 같습니다.  
그리고 화면과 background.js 사이에서 message를 주고 받는 것이 제대로 된 방법으로 생각 됩니다.  

하지만 첫 개발이다보니 초반에는 그 생각을 못했고 당장은 구조를 뜯어고치지 못해서 중요하지 않은 영역에 한해서 setTimeout을 사용 중입니다.  

<br>

Chrome과 Firefox의 차이가 생각보다 많다보니  
설계시에 브라우저 API를 호출하는 영역과 그 외 업무내용만 담고있는 영역을 분리하는 것이 중요합니다.  
저의 경우는 초반에 Chrome과 Firefox의 차이를 몰랐지만 습관적으로 영역을 분리시켜 둔 덕에 porting 할 때 그나마 편하게 작업할 수 있었습니다.  


#### References 

  * [Browser Extensions](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts)

  * [extesion-messaging](https://developer.chrome.com/extensions/messaging) 

  * [accessing-current-tab-dom-object-from-popup-html](https://stackoverflow.com/questions/1964225/accessing-current-tab-dom-object-from-popup-html)

  * [Firefox extension 만들기](http://i5on9i.blogspot.com/2018/02/firefox-extension.html)




