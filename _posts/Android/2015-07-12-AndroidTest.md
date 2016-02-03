---
layout: post
title:  "Test Methods Of Android"
date:   2015-07-12 16:00:00
categories: Android
tags: Virtual-Machine Genymotion IntelliJ
---

Android 개발을 해보면서 느꼈던 가장 큰 어려움은 테스트였습니다.  
테스트하는데 시간도 오래걸리고 다양한 디바이스도 필요하고 테스트 할 때는 항상 사람의 손이 필요하고..

그 중에서 테스트 시간과 다양한 디바이스에 대한 어려움을 약간은 해소시켜준 것이 Virtual Machine 입니다.

<!--more-->

# Packet Trace

## References
  * [capturing-mobile-phone-traffic-on-wireshark](http://stackoverflow.com/questions/9555403/capturing-mobile-phone-traffic-on-wireshark)


# Virtual Machine
AndroidStudio 의 AVD을 사용해보려했으나 BIOS 설정에 부딪혔었습니다.  
재부팅을 할까? 하다가 예전에 Eclipse AVD을 사용하며 성격을 갉아먹은 추억이 떠오르며 일단 공기계로 테스트 진행했었습니다.  
테스트를 완료하고 자신만만하게 지인의 폰에 설치를 시도했으나 설치 단계에서 실패하는 민망한 상황 발생해버렸죠.  
IDE와 별개로 VM을 구성해서 다양한 디바이스 테스트를 진행하기로 다시 계획 중입니다.

  * Ubuntu 14.04 LTS 에서 진행

## Install Virtual Box & Genymotion 

### References
  * [install-genymotion-in-ubuntu-14-04](http://sysads.co.uk/2014/06/install-genymotion-in-ubuntu-14-04/)


## Install the Genymotion plugin in IntelliJ 
단순히 자신의 apk 파일을 drag and drop 만으로도 VM에 app을 설치 & 실행 가능합니다.  
하지만 테스트 단계이기 때문에 에러로그를 보는 것이 필수죠.  
IntelliJ의 Genymotion plugin 으로 연동해서 app을 설치 & 실행 시키고 로그는 IntelliJ 를 통해 보는 것으로 진행하기로 합니다.

### References
  * [Genymotion Drag And Drop](https://www.genymotion.com/#!/)
  * [Genymotion plugin 설치 및 설정](http://webnautes.tistory.com/461)

## Technical Issues

### VT-x is diabled in the BIOS 
  * Genymotion 에서 mobile VM 을 구동했으나 실패 
  ![msg_genymotion_1.png](/images/post_img/Android/Test/msg_genymotion_1.png)
  Error message : Unalbe to start the virtual device. VirtualBox cannot start the virtual device.

  * VirtualBox 에서 직접 mobile VM 구동시도했으나 실패
  ![virtualbox_android.png](/images/post_img/Android/Test/virtualbox_android.png)
  ![msg_virtualbox_android_1.png](/images/post_img/Android/Test/msg_virtualbox_android_1.png)
  Errexeor message : VT-x is diabled in the BIOS (VERR_VMX_MSR_VMXON_DISABLED).

  * 재부팅해서 BIOS 설정 변경해준 후 재시도하니 성공

#### References
  * [VT-x is diabled in the BIOS](http://roadrunner.tistory.com/81)

### Resource lack
Genymotion으로 mobile VM을 하나 띄운 채로 AndroidStudio에서 app을 구동했더니 컴퓨터가 멈춰버렸습니다.  
재부팅 후 AndroidStudio에서 app을 구동하니 mobile VM이 자동으로 띄워집니다.  
제 컴퓨터의 리소스 문제일 것 같은 느낌이 드네요.




