---
layout: post
title:  "Google IO Extended Seoul"
date:   2015-07-12 13:00:00
categories: Conference
---
  * 장소 : 세종대학교 광개토관

# Android M Preview Version
## Authorization
### Process
  * 메니페스트 상에 권한 설정
  * check Self Permision
  * shouldShowRequestPermissionRationable (optional)
  * requestPermissions
  * onRequestPermissionResult

### 변경사항
  * 기존에는 앱 설치시 권한에 대한 사용자 수락이 이루졌음
  * 최초 모듈의 기능을 수행시 권한에 대한 수락이 필요해짐
  * M 보다 낮은 버전으로 빌드 된 app은 기존처럼 수행됨
 
  * Android 설정에서 app들의 권한을 설정하는 메뉴 추가됨
  - 사용자가 권한을 제거하면 에러를 내진 않고 엉뚱한(empty or success) 결과값을 반환함

  * 몇몇 권한은 normal 레벨로 낮아질 예정
  * 설치, 업데이트 시에는 권한에 대한 경고메시지 출력안됨

## 베터리 효율을 위한 개선
### 베터리에 대한 하드웨어 차원에서의 이해
  * 특정 칩셋이 Standby 모드일 땐 베터리 사용량이 거의 없음
    - Standby, low power, full power mode
    - 17 초 정도 대기하다가 지속적으로 사용되지 않을 경우 standby 모드로 변경됨
  * app의 불필요한 작업을 최소화해서 칩셋이 잠들 수 있도록 유도

### Doze Mode
  * 베터리 효율을 개선하기 위한 모드

### 변경사항
  * Doze mode, App 대기 mode 추가
  * Doze mode : device 가 잠겼을 때 
    - 네트워크 작업 불가, 백그라운드 작업 불가, Wakelock 무시됨, Wifi scan 멈춤
    - GCM 을 통해 앱을 잠깐 꺠울 수 있음
    - Idle, Idle_maintenance 단계를 반복함
  * App 대기 mode : app

  * Active, Inactive, Idle_pending, Sensing, 
    - Idle 
    - Idle_maintenance : 잠깐씩 앱을 깨움

## 자동 백업, 복원
### 백업 위치 
  * Google Drive
  * 용량제한 : App 당 25 MB
  * 저장공간은 무료

### 백업 시점
  * 기기가 유휴 상태
  * 충전 중
  * Wifi 연결시

### 기타
  * M 버전 빌드인 경우에만 작동함
  * 데이터의 백업 필요여부는 XML 형식으로 설정가능

### 주의점
  * 개인정보
  * 디바이스 의존적인 정보는 백업하지 말것

## App Link
  * Contents를 열어보려고 할 때 어떤 app을 이용해서 수행할지 선택하는 과정
  * 메니페스트와 사이트에 설정하게 되면 사용자가 접속에 사용할 app을 선택할 필요없음

# Build
## Gradle
### Groovy language (dynamic)
  * task 간의 dependency 설정으로 수행 순서를 설정가능
  * jcenter : android의 경우는 mavencenter가 아닌 Jcenter를 사용

### DSL (static)

## New Android Plug-in (Experimental)
  * Gradle 2.5 버전에 맞춘 plug-in

## Jack & Jill
  * 가을쯤 정식버전 런칭 예정
  * Java Android Compiller Kit
  * Library 에 대한 빌드툴 
  * DEX 에서 돌아갈 수 있는 코드로 바로 컴파일해줌

#### References
  * http://github.com/googlesamples/android-ndk

# Systrace 
  * Android 측정도구

# AngularJs & Firebase 로 Web 서비스 구축
## Firebase
  * BasS (Backend-as-a-Service)
    - Facebook's Parse
### Feature
  * Realtime Database
  * User 관리가 필요없음
  * static data
### Max Connection
  * Rest API 를 사용하면 connection 체크가 안됨
   - 대신 Realtime Database 는 안됨


#### References
