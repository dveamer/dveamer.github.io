---
layout: post
title:  "Setting Android Studio"
date:   2020-04-14 0:00:00
categories: Android
tags: Android Setting
---

![AndroidStudio](https://developer.android.com/studio/images/debug/layout-inspector_2x.png?hl=ko){:class="imgTitle"}  
( 이미지 출처 : [https://developer.android.com/](https://developer.android.com/) )  

이 글은 Ubuntu 환경에서 Android Studio를 설치하고 설정하는 방법을 설명합니다.  
특히 기존에 Google Play에 등록해둔 앱을 새로운 Ubuntu & Android Studio 환경에서 개발하기 위해 개발환경을 구축하는 분들을 위한 글입니다.  
Android 앱 개발에 대한 내용은 다루지 않습니다.  

<!--more-->


# Environment

  * Ubuntu 18.04.LTS

# ADB 구성

ADB(Android Debug Bridge)가 설치되어있어야 테스트 디바이스에 배포를 할 수 있습니다.  

### ADB 설치

~~~terminal
$ sudo apt-get install adb
~~~

### 다바이스 검색

컴퓨터에 USB로 디바이스를 연결 후 연결된 디바이스를 검색해봅니다.  

~~~terminal
$ adb devices
~~~

디바이스가 검색이 되어있지 않다면 다음과 같은 원인이 있을 수 있습니다.  

  * USB가 제대로 연결되지 않음  
  * 충전만 가능한 USB 사용  
  * ADB가 제대로 설치 되지 않음 혹은 ADB server restart 필요  
  * 디바이스의 ```개발자 옵션```이 off 인 상황
    - Android 8.0 and higher : Settings > System > About phone 으로 이동해서 Build number를 7번 누릅니다.  
    - Android 4.2 ~ 7.1.2 : Settings > About phone 으로 이동해서 Build number를 7번 누릅니다.  
  * 디바이스의 USB 디버깅이 off 인 상황
    - ```개발자 옵션```으로가서 ```USB 디버깅``` 항목을 on 시킵니다.  


디바이스가 검색은 됐는데 ????????? 같은 표시와 함께 권한이 없다는 메시지가 뜨면 다음과 같은 원인이 있을 수 있습니다.  

  * 디바이스 USB 디버깅 권한을 제공안한 경우
    - 디바이스에서 권한을 제공하겠냐는 알림이 떴을 때 제공해줍니다.  

# JDK 구성

JDK 가 설치되어있는지 체크합니다.  

~~~terminal
$ java -version
~~~

### JDK 설치

JDK가 설치되어있지 않다면 JDK를 설치해줍니다.  

~~~terminal
$ sudo apt install openjdk-11-jdk
~~~

# gradle 구성

gradle 이 설치되어있는지 체크합니다.  

~~~terminal
$ gradle -version
~~~

### gradle 설치

gradle이 설치되어있지 않다면 gradle을 설치해줍니다.  

~~~terminal
$ sudo add-apt-repository ppa:cwchien/gradle
$ sudo apt search gradle
~~~

원하시는 버전을 찾아서 아래와 같이 설치해주시면 됩니다.  

~~~terminal
$ sudo apt install gradle-5.6.4
~~~

# Android Studio 구성

Android Studio는 IntelliJ IDEA 기반의 Android 앱 개발을 위한 IDE 입니다.  

## PPA 저장소 추가 & 업데이트 

~~~terminal
$ sudo add-apt-repository ppa:maarten-fonville/android-studio
$ sudo apt-get update
~~~

## Android Studio 설치

~~~terminal
$ sudo apt-get install android-studio
~~~

### 실행

~~~terminal
$ /opt/android-studio/bin/studio.sh
~~~

### Desktop Entry

필수 작업은 아니지만 Android Studio를 쉽게 실행하기 위해서 ```Tools > Create Desktop Entry```를 선택합니다.  
다음부터는 Window 키 누르신다음에 Android Studio 검색하시면 클릭만으로 접속하실 수 있습니다. 그리고 favorites에 등록하실 수 있습니다.  

## 테스트 프로젝트

테스트 프로젝트를 생성해서 테스트 폰에 앱을 배포해서 설정에 문제가 없는지 체크합니다.  

### 프로젝트 생성

임의의 프로젝트를 생성합니다. 프로젝트 템플릿을 선택하라고하면 basic activity를 선택하시고 프로젝트 명을 입력하라고하면 기본 값으로 생성합니다.  

### Build & Deploy To A Device

```Run > Edit Configuration``` 에 들어가셔서 왼쪽 상단의 ```+``` 버튼을 클릭하신 후 ```Android App```을 선택합니다.  
그 뒤 ```Module```이 ```<no module>```로 되어있을 텐데 ```app```으로 변경해주시고 ```OK``` 버튼을 클릭해주시면 됩니다.  

그 후 ```Run > Run```을 눌러보시면 빌드 & 배포가 진행되고 디바이스에서 앱이 실행되는 것을 확인하실 수 있습니다.  


# 기존 앱 관련 설정

여기서부터는 기존에 Android 앱을 개발해서 Google Play에 등록하셨던 분들을 위한 내용입니다.  
기존 소스를 clone하고 IntelliJ에서 open 하는 과정은 생략하겠습니다.  

## SDK Manager

```File > Settings```에 들어가서 ```Appearance & Behavior > System Settings > Adnroid SDK``` 를 찾아가보시면 Android의 다양한 버전이 나열 되어있습니다. 그 중 status가 intalled인 버전이 있고 Not installed인 버전들이 있는데 만들어둔 앱 ```app/build.gradle``` 의 compile, target SDK version 은 installed 상태로 만들어주셔야 빌드가 가능합니다.  


```app/build.gradle``` 

~~~gradle

...(생략)

    compileSdkVersion 28
    ...(생략)

    defaultConfig {
        ...(생략)
        minSdkVersion 19
        targetSdkVersion 28
        ...(생략)
    }

...(생략)
~~~

## Gradle Plugin

[gradle-plugin](https://developer.android.com/studio/releases/gradle-plugin.html) 문서의 프러그인 버전과 필요한 gradle 버전이 맵핑되어있는 테이블을 참고해서 플러그인 버전을 ```gradle-wrapper.properties```에 gradle버전을 ```gradle-wrapper.properties```에 입력합니다. 가장 최신의 버전으로 적용하시면 되고 gradle sync를 맞추면 gradle이 관련 의존성 파일들을 다운받는 것을 보실 수 있습니다.  

만약에 가장 최신 버전으로 했을 때 문제가 생기신다면 적절히 낮은 버전으로 적용하시고 진행해보세요. 의존성 파일들을 다운 받고 난 뒤 IntelliJ가 최신 gradle plugin 이 있다는 메시지와 함께 업그레이드를 권할 것입니다. 그 때 업그레이드 하시면 됩니다.  


```gradle-wrapper.properties```
~~~properties
#Mon Apr 13 20:28:01 KST 2020
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-5.6.4-all.zip
~~~



```build.gradle```

~~~gradle
buildscript {
    repositories {
        ...(생략)
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.6.1'

        ...(생략)
    }

    ...(생략)
}
~~~


## 확인작업

아까 테스트 프로젝트를 디바이스에 배포했던 것과 동일하게 빌드 및 배포를 해서 디바이스에서 앱이 정상적으로 작동하는 것을 확인하셔야 합니다.  

# Google Play 관련 설정

Google Play 배포를 위한 release 빌드를 했을 때 storeFile 관련한 에러메시지가 발생합니다. 이 것을 해결하기 위한 설정이 필요합니다.  

~~~terminal
$ gradle bundleRelease
~~~

만약 에러가 발생하지 않고 빌드가 성공한다면 [잘못된 build.gradle](#잘못된 build.gradle) 설정을 하신 것이 아닐까 의심이 됩니다.  

## keystore 복사해오기

예전에 Google Play에 앱을 등록하는 과정에서 만드신 keystore파일을 만드셨을 겁니다. 그 파일과 당시에 설정했던 패스워드 정보들이 필요합니다.  
그 keystore 파일을 새롭게 Android Studio를 구성하고 있는 Ubuntu로 복사해옵니다.  

준비하신 정보들이 아래와 같다고 생각하고 설명을 진행하겠습니다.  

  * KEYSTORE PATH : /MY_APPLICATION.store
  * KEYSTORE PASSWORD : store_1234
  * KEY ALIAS : MY_APPLICATION
  * KEY PASSWORD : key_1234

만약에 당시에 만들었던 그 파일이나 정보가 없다면 [Google Play 고객센터](https://support.google.com/googleplay/android-developer/answer/7384423?hl=ko)를 참고하셔서 키 재설정을 하셔야 합니다. 몇시간 혹은 몇일의 시간이 소요되실 것으로 예상됩니다.  

## build.gradle 확인

```app/build.gradle``` 파일을 열어보시면 아래와 같이 설정을 해두셨을 겁니다.  

~~~gradle

...(생략)

    signingConfigs {
        release {
            storeFile file(System.getenv("ANDROID_KEYSTORE_PATH"))
            storePassword System.getenv("ANDROID_KEYSTORE_PSWD")
            keyAlias System.getenv("ANDROID_KEY_ALIAS")
            keyPassword System.getenv("ANDROID_KEY_PSWD")
        }
    }

    buildTypes {
        release {
            signingConfig signingConfigs.release
            ...(생략)
        }
    }

...(생략)

~~~

여기서 중요한 것은 아래와 같은 환경변수 명입니다.  

  * ANDROID_KEYSTORE_PATH
  * ANDROID_KEYSTORE_PSWD
  * ANDROID_KEY_ALIAS
  * ANDROID_KEY_PSWD

### 잘못된 build.gradle

만약에 ```app/build.gradle```파일을 열었는데 아래처럼 패스워드 정보가 직접 입력되어있다면 보안에 취약한 상황이니 바로 수정하시기 바랍니다.  

```app/build.gradle``` 

~~~gradle

...(생략)

    signingConfigs {
        release {
            storeFile file("/MY_APPLICATION.store")
            storePassword "store_1234"
            keyAlias "MY_APPLICATION"
            keyPassword "key_1234"
        }
    }


    buildTypes {
        release {
            signingConfig signingConfigs.release
            ...(생략)
        }
    }

...(생략)
~~~

이와 같은 상태로 GitHub 같은 공개된 repository에 소스가 올라가있다면 굉장히 위험한 상태입니다. 파일 수정만이 아니라 해당 repository는 삭제 후 다시 생성하셔야 합니다.  


## 환경변수 설정

```app/build.gradle``` 파일에 설정된 환경변수 명에 맞춰서 실제 환경변수를 설정해봅니다.  

아래의 스크립트를 적절하게 수정하신 후 순서대로 실행해주시면 됩니다.  

환경변수 명(예:ANDROID_KEYSTORE_PATH)도 수정하시고 환경변수 값(예:/MY_APPLICATION.store)도 적절하게 수정하시면 됩니다.  

~~~terminal
$ echo '# Android App Environment' >> ~/.profile
$ echo 'export ANDROID_KEYSTORE_PATH="/MY_APPLICATION.store"' >> ~/.profile
$ echo 'export ANDROID_KEYSTORE_PSWD="store_1234"' >> ~/.profile
$ echo 'export ANDROID_KEY_ALIAS="MY_APPLICATION"' >> ~/.profile
$ echo 'export ANDROID_KEY_PSWD="key_1234"' >> ~/.profile
~~~

환경변수 설정 후 Ubuntu 로그인을 다시 하신 후 IntelliJ를 사용하셔야 합니다.  


## 확인작업

앱 버전을 올려서 빌드 후 Google Play에 등록을 해보시고 정상적으로 등록되는지 확인해봅니다.  










