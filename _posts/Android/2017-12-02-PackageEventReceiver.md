---
layout: post
title:  "Android 앱 설치 / 업데이트 이벤트 처리"
date:   2017-12-02 01:00:00
categories: Android 
tags: Android BroadcastReceiver
---

![Android Logo](https://developer.android.com/_static/images/android/touchicon-180.png){:class="imgTitle"}  
(이미지 출처 : [https://developer.android.com](https://developer.android.com))  

Google Play와 같은 스토어를 통해서 앱이 설치 / 업데이트 되었을 때, 그 이벤트를 알림받고 처리하는 방법에 대해서 설명합니다.  

그리고 특정 앱의 이벤트만 알람을 받는 방법도 알아보고 관련해서 제가 겪은 당혹스러운 사건도 적어보겠습니다.  

업데이트 이벤트 처리는 인터넷 검색하면 쉽게 찾을 수 있는 정보지만,  
검색된 내용대로 따라서 코딩하다보면 개발 당시에는 문제를 못느끼다가  
앱 공개 후 저와 비슷한 경험을 하게 될 것 같아서 글을 작성합니다.  

<!--more-->


# BroadcastReceiver 추가하기  

BroadcastReceiver는 Android 시스템으로부터 broadcast intent를 전달받고 처리할 수 있는 추상객체(abstract class)입니다.  
BroadcastReceiver를 통해서 Android 시스템으로부터 앱 패키지 설치 / 업데이트 이벤트를 전달받고 처리하고자 합니다.  

BroadcastReceiver를 사용하기 위해서는 아래 두 과정이 필요합니다.  

  * BroadcastReceiver 구현체 생성  
  * BroadcastReceiver 구현체 등록  

## BroadcastReceiver 구현체 생성  

BroadcastReceiver의 구현체 PackageEventReceiver 만듭니다.  

~~~java
package your.app.pakcage.sample;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;

public class PackageEventReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();

        if(action.equals(Intent.ACTION_PACKAGE_REPLACED)){
            // Broadcast Action: A new version of an application package has been installed, replacing an existing version that was previously installed. 
            // 새로운 버전의 앱 패키지가 설치 되거나 업데이트 되었을 때 

            // do something...
        }
    }
}

~~~

앱이 설치, 업데이트 되었을 때 처리하고 싶은 내용을 IF문 안에 기술하시면 됩니다.  


## BroadcastReceiver 구현체 등록

구현체 등록방법은 동적인 방법과 정적인 방법이 있습니다.  

동적인 방법은 실행된 Activity 혹은 Service 같은 다른 class에서 등록해주는 방법입니다.  
Java 코드로 등록 과정을 기술하면서 상황에 따라 다르게 등록할 수 있습니다.  


앱 패키지 설치, 업데이트 이벤트의 경우는 정적인 방법이 더 어울리므로  
AndroidManifest.xml 을 통한 등록방법만 기술하겠습니다.  

~~~xml

<?xml version="1.0" encoding="utf-8"?>
<manifest ...
>
  ...

  <application
          ...
  >
    ...

    <receiver android:name=".sample.PackageEventReceiver"> 
        <intent-filter>
            <action android:name="android.intent.action.PACKAGE_REPLACED"/>
            <data android:scheme="package" android:path="your.app.pakcage"/>
        </intent-filter>
    </receiver>
    
    ...

  </application>

  ...

</manifest>

~~~


```manifest > application``` 아래에 ```receiver```를 추가합니다.  

```receiver android:name```에는 위에서 생성한 PackageEventReceiver의 정보를 적어주면 됩니다. (변경필요)  

```intent-filter > action android:name```에는 처리하고자하는 intent action 을 적어주면 됩니다. (필요시 추가 필요)  

```intent-filter > data android:path```에는 만들고 있는 앱의 package 명을 적어주시면 됩니다. (변경필요)  

## 주의사항

보통 자신이 만들고 있는 앱이 업데이트 되었을 때 그 이벤트를 받아서 처리하기 위해서 이 글을 검색하셨을 겁니다.  
근데 BroadcastReceiver는 모바일에 설치되어있는 모든 앱에 대해서 앱 업데이트가 일어날 때마다 이벤트를 받게 됩니다.  
자신이 만든 앱의 이벤트만 알림을 받고 싶다면 아래와 같이 구현체에서 조건문으로 처리해야합니다.  

~~~java
package your.app.pakcage.sample;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;

public class PackageEventReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();

        if(action.equals(Intent.ACTION_PACKAGE_REPLACED)){
            // Broadcast Action: A new version of an application package has been installed, replacing an existing version that was previously installed. 
            // 새로운 버전의 앱 패키지가 설치 되거나 업데이트 되었을 때 

            if (intent.getDataString().contains("your.app.pakcage")){
                // do something...
            }
        }
    }
}

~~~


이와 관련해서 저는 굉장히 당혹스러운 사건을 겪었습니다.  

앱 업데이트라는 것이 자주 일어나는 것이 아니기 때문에 개발 당시에는 발견하지 못했고  
앱 설치수가 1만이 넘어가고나서야 이상함을 눈치채고 확인해볼 수 있었습니다.  

AWS 네트워크 비용이 예상보다 많이나와서 살펴보니  
사용자별로 하루 1회 혹은 앱 업데이트시 1회 실행되도록 설계한 API를 하루에 100회 이상 호출하는 사용자들이 있었습니다.  
(그래봐야 지난달에 3만원정도의 수준입니다. 걱정해주지 않으셔도 괜찮습니다 ^^ )  

처음에는 제 코드에 문제가 있나 하고 코드를 이리저리 살펴보고 테스트해보느라 삽질을 했습니다.  
그리고는 "HttpConnection 객체가 retry하는 기능이 있나?" 하고 검색을해보니 slient retry 라는 이슈글이 몇개 있더군요. 그래서 열심히 파봤지만 삽질이었습니다.  

요즘 사용자의 모바일 스팩이 대부분 좋아서 수백개의 앱을 사용하고 Google Play의 자동 업데이트 기능을 이용합니다.  
많은 앱들의 업데이트가 몰리는 날에는 해당 API가 100회 이상 호출되는 상황이었던 것입니다.  

그래도 저는 서버로 호출하는 내용이 있어서 발견을 했지만  
서버와 교류없이 내부에서만 처리하는 앱이라면 발견하기 굉장히 힘들 것 같네요.  

사실 BroadcastReceiver를 이해하고 있었다면 당연히 피했어야할 상황이지만  
귀찮다고 다른 한글 블로그의 설명을 생각없이 따라가다보니 겪은 사건이었습니다.  
Android의 공통 action을 사용할 때는 좀 더 자세히 살펴봐야할 필요가 있는 것 같습니다.  

# 다른 Package Actions 

지금까지 앱 패키지 설치 / 업데이트 이벤트를 처리하기 위해서  
Android 시스템으로부터 ACTION_PACKAGE_REPLACED 라는 boradcast intent를 받아서 처리하는 내용을 살펴봤습니다.  

앱 패키지 업데이트 이벤트만이 아니라 다양한 이벤트들을 전달 받을 수 있습니다.  
필요시에는 BroadcastReceiver 구현체 구현, 등록 과정에서 필요한 action을 추가해서 사용하시면 됩니다.  


## Package Actions

  * ACTION_PACKAGE_ADDED
    Broadcast Action: A new application package has been installed on the device.

  *	ACTION_PACKAGE_CHANGED
    Broadcast Action: An existing application package has been changed (for example, a component has been enabled or disabled).
  
  *	ACTION_PACKAGE_DATA_CLEARED
    Broadcast Action: The user has cleared the data of a package.

  * ACTION_PACKAGE_FIRST_LAUNCH
    Broadcast Action: Sent to the installer package of an application when that application is first launched (that is the first time it is moved out of the stopped state).

  * ACTION_PACKAGE_FULLY_REMOVED
    Broadcast Action: An existing application package has been completely removed from the device.

  * ACTION_PACKAGE_INSTALL
    This constant was deprecated in API level 14. This constant has never been used.

  * ACTION_PACKAGE_NEEDS_VERIFICATION
    Broadcast Action: Sent to the system package verifier when a package needs to be verified.

  *	ACTION_PACKAGE_REMOVED
    Broadcast Action: An existing application package has been removed from the device.

  *	ACTION_PACKAGE_REPLACED
    Broadcast Action: A new version of an application package has been installed, replacing an existing version that was previously installed.

  * ACTION_PACKAGE_RESTARTED
    Broadcast Action: The user has restarted a package, and all of its processes have been killed.

  *	ACTION_PACKAGE_VERIFIED
    Broadcast Action: Sent to the system package verifier when a package is verified. 


#### References
  * [https://developer.android.com - Intent](https://developer.android.com/reference/android/content/Intent.html#ACTION_PACKAGE_REPLACED)
  * [https://developer.android.com - BroadcastReceiver](https://developer.android.com/reference/android/content/BroadcastReceiver.html)


