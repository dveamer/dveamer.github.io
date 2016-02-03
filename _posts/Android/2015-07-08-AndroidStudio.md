---
layout: post
title:  "Android Studio"
date:   2015-07-08 22:00:00
categories: Android
tags: IDE Android-Studio
---
![AndroidStudio](http://1.bp.blogspot.com/-UGrENgc-ec8/VIJsFPD19aI/AAAAAAAABBk/ICFczO1O6mU/s1000/studio-logo.png)

( 이미지 출처 : [http://1.bp.blogspot.com](http://1.bp.blogspot.com) )

<!--more-->

# Previous Tasks
  * Install JDK Software
  * Set JAVA_HOME

# Shortcut Keys
 * http://www.androidside.com/bbs/board.php?bo_table=B56&wr_id=26482

# Install Android Studio



# References For Future
  * [에뮬레이터 실행속도 올리는 법](https://www.davidlab.net/ko/tech/how-to-setup-android-dev-env-on-ubuntu-part2/)

# Happened Troubles

## KVM is not installed on this machine
  * 에뮬레이터를 동작시키는 과정에서 아래와 같은 에러 메시지 발생

~~~
/home/mret/Android/Sdk/tools/emulator -netdelay none -netspeed full -avd Nexus_5_API_22_x86
emulator: ERROR: x86 emulation currently requires hardware acceleration!
Please ensure KVM is properly installed and usable.
CPU acceleration status: KVM is not installed on this machine (/dev/kvm is missing).
~~~

### Reason
  * CPU 가 VT(Virtualization Technology) 을 지원해야함 : 지원함
  * BIOS 에서 VT를 활성화 시켜야함 : 미확인
  * KVM 이 설치되어야함 : 설치
  * 관련 패키지가 설치되어야함 : 설치

### Solution
  * KVM 을 설치하고 관련 모듈을 설치 시도했으나 쉽게 풀리지 않을 것을 것으로 예상되서 중단

~~~
sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils
~~~

  * 앱의 기본적인 모양이 나올 때까진 에뮬레이터를 사용하지 않기로함
  * 핸드폰을 유선으로 연결해서 테스트 진행

### References
  * https://help.ubuntu.com/community/KVM/Installation
  * [에뮬레이터 실행속도 올리는 법](https://www.davidlab.net/ko/tech/how-to-setup-android-dev-env-on-ubuntu-part2/)

## Failure [INSTALL_FAILED_OLDER_SDK]

### Reason
  * 핸드폰보다 상위의 Android 버전에 적합하게 컴파일되서 생기는 현상

### Solution
  * Tools > Android > SDK Manager 에서 필요한 패키지 다운로드
    - 기본적으로 선택되는 패키지 모두 다운로드
    - 핸드폰 버전과 동일한 SDK 패키지 다운로드
  * Min SDK Version 변경
    - 프로젝트 우클릭 > Open Module Setting > Flavors > Min SDK Version 변경
    - 핸드폰 버전보다 낮게 변경

### References
  * http://blog.daum.net/_blog/BlogTypeView.do?blogid=0qFz3&articleno=3&_bloghome_menu=recenttext


