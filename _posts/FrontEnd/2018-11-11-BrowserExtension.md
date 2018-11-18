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

Firefox 와 Chrome을 비교하는 형태로 글을 작성하겠습니다.  

# 확장앱 개발자모드 

앱을 등록하기 전에 브라우저에서 테스트가 가능합니다. 아래 주소를 복사해서 브라우저 주소창에 입력해주세요.  

## Firefox 

Firefox 브라우저 주소창에 입력하세요.  

  * 개발 중인 확장앱 : "about:debugging"
  * 정식 확장앱 : "about:addons"

## Chrome

Chrome 브라우저 주소창에 입력하세요.

  * 정식 확장앱 : "chrome://extensions"

# 확장앱 등록하기

아래 링크로 이동해서 가입하시면 확장앱을 정식 스토어에 등록 합니다.  


## Firefox

  * [앱 등록 https://addons.mozilla.org/ko/developers](https://addons.mozilla.org/ko/developers)  
  * [압축하는 방법](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Package_your_extension_)  

Firefox는 등록이 매우 간단합니다. 만들어둔 zip 파일, 이미지 1장 그리고 앱의 간략한 설명만 적어주면 등록이 가능합니다.  
게다가 등록요청 후 10분 이내에 등록이 완료됩니다. 등록처리되었다는 메일도 주며 정식 앱을 바로 설치 가능합니다.  
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

  * [앱 등록 https://chrome.google.com/webstore/developer/dashboard](https://chrome.google.com/webstore/developer/dashboard)  
  * [앱 등록 - 신규페이지 https://chrome.google.com/webstore/devconsole](https://chrome.google.com/webstore/devconsole)

Firefox와 달리 필요한 준비물이 많고 구체적입니다.  

크롬은 최초 1회 5$ 비용이 발생합니다. 국제결제가 가능한 신용카드가 필요하니 준비하시기 바랍니다.  

zip파일 외에도 홍보용 이미지, 스캔 이미지 등 준비가 필요합니다. 게다가 이미지의 사이즈, 여백 사이즈가 정해져있습니다.  
확장 앱을 만드는 과정에서 확정된 이미지를 만들면 등록 과정에서의 태클로 인해 정신적 데미지를 입으실 수 있습니다.  
앱 개발 초반에 먼저 확장 앱을 등록해보면서 이미지를 만들어나가시길 추천합니다.  

앱을 올리고 나면 "2~3일 이내에 점검 완료 후 등록 예정" 이라는 메시지가 뜹니다.  
제가 만든 확장앱이 사용중인 tab의 HTML에 대한 조회 권한이 있어서 인지는 모르겠으나..  
등록하는데 걸리는 시간이 3일 이상 걸립니다. 처음에는 일요일 밤에 등록해서 수요일에 완료됐지만 두번째는 토요일 밤에 등록해서 목요일 밤인 현재도 완료되지 않았습니다. 아직 검토 중이라고 뜨네요. 메일이라도 주면 좋을텐데 그렇지 않아서 매일 한번씩 직접 확인을 해야합니다.  

그리고 등록을 기다리는 도중에 추가변경 건이 생겨도 기다려야 합니다. 제 경우에는 0.10 버전을 올리고 기다리다가 다음 버전으로 올리고 싶어서 0.10 버전을 취소시키고 다음 버전을 등록시켰습니다. 다음 버전에 대한 "검토 대기 중"이라는 문구도 확인하고 기다렸는데 4일이 지나서 0.10 버전으로 등록이 완료된 것을 확인했습니다. 좀 당황스러웠습니다.  

# API

당연한 이야기지만 API가 다릅니다.  

```sendMessage``` 라는 API를 예로 들면, chrome은 ```chrome.extension.sendMessage({});```, Firefox는 ```browser.runtime.sendMessage({});```와 같이 사용합니다.  

대부분 Chrome은 ```chrome```으로 시작하고 Firefox는 ```browser```로 시작합니다.  

파리미터 개수가 다른 경우도 많습니다.  
Firefox는 성공, 실패 두가지 경우의 callback function을 파라미터로 받는 반면,  
Chrome은 성공에 대한 callback function만 파라미터로 받는 경우가 대부분 입니다.  


"어떤 API가 더 좋다" 는 것은 잘 모르겠고..  

Chrome과 Firefox의 차이가 생각보다 많다보니  
설계시에 브라우저 API를 호출하는 영역과 그 외 업무내용만 담고있는 영역을 분리하는 것이 중요한 것 같습니다.  
저의 경우는 초반에 Chrome과 Firefox의 차이를 몰랐지만 습관적으로 영역을 분리시켜 둔 덕에 porting 할 때 그나마 편하게 작업할 수 있었습니다.  


# 제공 기능

제공되는 기능에 차이가 있는 경우가 있습니다.  

예를들어,  

Firefox는 ```webRequest```를 이용해서 intercept하고 logging만이 아니라 blocking, redirect가 가능합니다.  
하지만 Chrome은 ```webRequest``` 으로는 intercept 후 logging만 가능합니다.  

대신 chrome에서는 ```declarativeWebRequest```을 따로 제공하고 제한적인 blocking은 가능합니다.  
하지만 이 기능도 beta channel에서만 해당 기능이 가능합니다.  
저의 경우는 Ubuntu에서 Chrome Chromium 브라우저로 테스트를 마쳤던 ```declarativeWebRequest``` 기능이 정식 앱 등록 후에 Windows의 Chrome 브라우저에서 테스트를 했더니 ```declarativeWebRequest```는 beta channel에서만 사용 가능하다는 메시지가 출력됐습니다.  

# UX

UX 측면에서는, 파폭은 팝업 화면에서 마우스를 벗어나면 팝업이 자동으로 닫히게 되는데 크롬은 팝업을 닫는 액션을 취하지 않는한 계속 유지됩니다.  

제 확장앱의 경우 가입할 때 e-mail 주소와 OTP를 입력받는 내용이 있습니다.  
Firefox 확장 앱 팝업 화면에서 e-mail 주소를 입력 후, 서버에서 전송한 OTP를 확인하기 위해 다른 탭에서 메일을 확인하고 오면 확장 앱 팝업이 사라집니다. 그리고 팝업을 다시 열었을 때는 아까 입력해뒀던 e-mail 주소가 화면에서 지워지게 됩니다. 만약에 e-mail 주소를 화면에 유지시키고 싶다면 데이터를 storage에 임시적으로 보관하는 등의 방법을 취해야합니다.  


# 테스트 

Chrome이 더 엄격하고 테스트하기 편합니다.  

Chrome은 실패에 대한 callback function이 없지만 에러가 발생하면 runtime 에러처리 되어 기능이 정지하고 개발자 모드에서 에러를 표시해줍니다.  
Firefox는 callback function만 실행시켜주고 나몰라라 하는 느낌입니다. 심지어 callback function 에서 에러가 발생하면 callback function의 정상 동작 여부를 파악할 방법이 없습니다. 매번 로깅을 하거나 데이터를 확인해서만 확인이 가능합니다.  

초반에는 Firefox에서 개발하고 Chrome으로 porting을 했습니다. 그러다보니 Firefox에서 잘 돌아간다고 생각했던 기능이 깔끔하게 마무리 되지 않고 있었던 것을 Chrome에서 확인하게 되는 경우가 많았습니다. 그래서 지금은 거꾸로 Chrome에서 개발하고 Firefox로 porting 중입니다.  

# Call-Back

Firefox는 callback의 시작시점도 애매합니다.  
storage.local에 데이터 저장을 요청하고 성공 callback function에서 ```location.reload();```로 화면을 재로딩 하는 경우를 예로 들겠습니다.  
일단 Firefox는 화면이 재로딩은 되지만 데이터는 저장되어있지 않습니다. 반면 Chrome은 재로딩과 저장이 모두 성공적으로 이뤄집니다.  
제 추측으로는 Firefox의 경우는 저장이 완전히 완료된 후에 callback function이 실행되는 것이 아닌 것 같습니다.  
callback function에서 ```location.reload();```를 setTimeout을 걸어서 1초 대기 후에 처리하면 저장과 재로딩이 모두 이뤄집니다.  

근데 사실 이것은 Firefox의 잘못은 아닌 것 같습니다.  
처음에 확장앱의 구조 자체를 제가 잘못 이해하고 진행한 것은 아닌가.. 라는 생각 중입니다.  

화면의 lifecycle 내에서 storage를 접근하는 것은 피하고 화면의 lifecycle과 무관한 background.js에서 storage를 접근하는 것이 맞는 것 같습니다.  
그리고 화면과 background.js 사이에서 message를 주고 받는 것이 제대로 된 방법으로 생각 됩니다.  

하지만 첫 개발이다보니 초반에는 그 생각을 못했고 당장은 구조를 뜯어고치지 못해서 중요하지 않은 영역에 한해서 setTimeout을 사용 중입니다.  

# 모바일

Chrome은 PC 브라우저만 확장앱을 사용할 수 있습니다.  
반면 Firefox는 모바일 브라우저에서도 확장앱을 사용할 수 있습니다.  

재미있는 점은,  
모바일은 확장 앱의 팝업 페이지가 제공되지 않습니다. UX가 PC 브라우저와 크게 차이가 나게 됩니다.  
그리고 확장 앱을 정식으로 등록하기 전까지 테스트를 할 방법이 없습니다.  

# 결론 

등록하는 과정에서는 Firefox가 Chrome보다 쉽고 빠르며 개발자 친화적입니다.  
제가 사용해본 범위 내에서는 Chrome에서 제공해주는 기능이 보다 제한적으로 느껴졌습니다.  

하지만 개발하는 과정에서 테스트도 Chrome이 편하고  
위에서 얘기한 등록과정이 길고 제공되는 기능이 제한적이라는 점도  
보안 측면에서 Chrome이 사용자에게 더 안전한 것 같습니다.  
개발자 입장에서 기능 구현에 제한이 들어가지만 길게 봤을 때 적절한 보안을 처리해주는 플랫폼이 더 유리하다고 생각됩니다.  

게다가 시장(?) 규모가 Chrome이 훨씬 크기 때문에 Firefox 보다는 Chrome을 메인으로 개발할 예정입니다.  

Firefox를 베타버전처럼 등록해서 실 사용을 하면서 테스트를 하고 Chrome에 안정적으로 등록해나갈 생각입니다.  

#### References 

  * [Browser Extensions](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts)

  * [extesion-messaging](https://developer.chrome.com/extensions/messaging) 

  * [accessing-current-tab-dom-object-from-popup-html](https://stackoverflow.com/questions/1964225/accessing-current-tab-dom-object-from-popup-html)

  * [Firefox extension 만들기](http://i5on9i.blogspot.com/2018/02/firefox-extension.html)




