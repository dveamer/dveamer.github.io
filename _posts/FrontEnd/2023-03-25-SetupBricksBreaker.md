---
layout: post
title: "Unity Bricks Breaker 에셋 설치하기"
date: 2023-03-25 00:00:00
lastmod: 2023-03-25 00:00:00
categories: FrontEnd
tags: FrontEnd Unity 
hidden: false
published: true
---

![Brick Breaker](https://assetstorev1-prd-cdn.unity3d.com/key-image/160a39ab-bf4c-42ea-984d-9ce11078fa9f.webp){:class="imgTitle"}  
(이미지 출처 : [https://assetstore.unity.com/](https://assetstore.unity.com/))  


[Brick Breaker](https://assetstore.unity.com/packages/templates/packs/template-bricks-breaker-175322) 라는 에셋을 구매했는데 설치과정에서 삽질을 좀 많이 했습니다.  
유니티 설치해본지도 2주 밖에 되지 않아서 기본 배경지식도 부족하고 첫 에셋 설치라 그런지 더 삽질을 하지 않았나 싶습니다.  

다른 분들과 나중에 또 설치할지 모를 미래의 저를 위해 기록을 남기고자 합니다.  

문제점을 간략하게 설명하면 다른 에셋과 의존관계 때문입니다.  

> This package requires an external plug-in. Refer to the Guide documentation included in the package.
> This asset uses the free DOTween / JSON .NET For Unity / spine-unity Unity 3.8 package asset.

가장 먼저 부딪힌 어렴움은 overview를 읽지 않고 진행했다는 점이었고 그 다음부터 겪은 어려움들을 적어보겠습니다.  

<!--more-->

## Unfortunately, JSON .NET For Unity is no longer available.

설치해야하는 JSON .NET For Unity 를 에셋스토어에서 검색했더니 더 이상 제공되지 않는다고 합니다. 확인해보니 직접 Newtonsoft.Json 라이브러리를 프로젝트에 추가하면 된다는군요.  

  * [NuGet 공식 웹사이트](https://www.nuget.org/packages/Newtonsoft.Json/)에서 Newtonsoft.Json 패키지를 찾은 다음 "Download package" 링크를 클릭하여 .nupkg 파일을 다운로드합니다.  
  * .nupkg 파일은 .zip 파일과 같은 압축 형식으로 압축을 해제한 후, "lib\netstandard2.0" 폴더에서 "Newtonsoft.Json.dll" 파일을 찾습니다.  
  * Unity 프로젝트에서 "Assets" 폴더 안에 "Plugins" 폴더를 생성하고 "Newtonsoft.Json.dll" 파일을 복사합니다.
  * Unity 에디터로 돌아가서 프로젝트가 자동으로 다시 컴파일되는지 확인합니다. 이제 Newtonsoft.Json 라이브러리가 프로젝트에 포함되어 오류가 해결되어야 합니다.
  * 그래도 안된다면 Unity 에디터에서 "Assets" > "Reimport All"을 클릭합니다.  

## spine-unity Unity 3.8 package asset 도 에셋 스토어에서 제공하지 않습니다.

이 경우는 overview에 파일다운로드 링크가 걸려있습니다. 다운받아서 실행하시면 유니티 패키지 매니저가 열리고 import 하시면 됩니다.  

저의 경우는, overview를 먼저 읽지 않고 다른 사용자가 남긴 리뷰에서 3.6 버전을 받아야 된다는 글을 보고 3.6 버전으로 시도하다가 안되서 가장 최신버전 4.1로도 시도하는 등 유니티 버전을 낮춰야하나? 고민도 해보고 이상한 짓을 계속 했습니다. 아마도 예전에는 3.6 버전에 맞춰져있었곘지만 그 이후 패치되면서 3.8로 바뀌었을 것 같습니다. 역시 공식 문서를 잘 읽어야한다는 교훈을 또 다시 떠올리게 됩니다.  

## Unity Ads 관련된 에러 

~~~
Assets\Bricks Breaker\Scripts\Common\_Manager\ADManager.cs(5,48): error CS0246: The type or namespace name 'IUnityAdsListener' could not be found (are you missing a using directive or an assembly reference?)
~~~

위와 같은 에러가 발생합니다. 
프로젝트의 Packages 폴더에 있는 manifest.json 파일을 열어보면 `com.unity.ads` 에 대한 의존성이 `4.4.1` 버전으로 설정되어있습니다. `3.7.1` 로 변경해줍니다.  

광고 기능을 정상적으로 이용하려면 나중에 4.4.1로 다시 3.7.1로 변경하고 ADManager.cs 파일을 4.4.1에 맞게 수정하거나 다른 광고 패키지를 활용하도록 변경해야할 수도 있습니다. 지금은 단순히 전체 설치를 완료하고 실행하기 위한 것을 목적으로 하기 떄문에 버전을 낮추는 방향으로 진행했습니다.  

그리고 그 외에도 Ads 관련된 에러들이 더 많이 발생했었습니다. 
이 에러들은 `Ads Mediation` 이라는 패키지를 설치했더니 사라졌는데 `com.unity.ads` 의 버전을 변경해주는 것만으로도 해결될 수 있지 않았을까 라는 생각이들기도 합니다.  
저는 `Ads Mediation`를 먼저 설치하고 `com.unity.ads` 의 버전을 변경해서 해결했기 때문에 어떤게 먼저 설치되어야 하는지는 잘 모르겠습니다.
 `Ads Mediation` 설치하는 방법은 아래와 같습니다.  

  * 유니티 에디터에서 Window > Package Manager를 선택합니다.
  * 왼쪽 상단에 있는 Packages 드롭다운 메뉴에서 Unity Registry를 선택합니다.
  * 목록에서 Advertisement Legacy를 찾아 선택한 다음, Install 버튼을 누릅니다.


## 실행

위의 내용들을 조치한 후에 `Assets\Bricks Breaker\Scense` 아래의 파일들을 더블 클릭한다음에 플레이 버튼 눌러 실행할 수 있었습니다.  


