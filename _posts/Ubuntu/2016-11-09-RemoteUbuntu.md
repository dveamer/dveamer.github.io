---
layout: post
title:  "Ubuntu VNC 원격접속"
date:   2016-11-09 12:00:00
categories: Ubuntu
tags: Ubuntu VNC Remote ssh-tunneling
---

![VNC Viewer](https://lh6.ggpht.com/RcRUeZKNRYaCfoNGMe8Ic8OORBN-_pXgNyNtvNfSQ-5DFl-7CTuTYC2m96BbbV5IQU0=w300){:class="imgTitle"}  
( 이미지 출처 : [VNC Viewer](https://play.google.com/store/apps/details?id=com.realvnc.viewer.android) )  

VNC를 이용해서 Ubuntu에 원격접속을 해봤습니다.  

ipTime의 DDNS를 이용해서 집 밖에서도 접속할 수 있도록 세팅했고  
ssh와 포트포워딩을 이용해서 암호화를 진행했습니다.  

<!--more-->

# Unencrypted VNC

Ubuntu 14.04 LTS에서 암호화하지 않은채로 원격접속을 허용하는 것은 굉장히 간단합니다.  

## Desktop Share

"데스크톱 공유(Desktop Share)"을 열고 아래 항목을 체크하고 비밀번호를 입력합니다.  
  - 다른 사용자가 데스크톱을 볼 수 있도록 합니다.
  - 다른 사용자가 데스크톱을 제어 할 수 있음
  - 사용자가 이 암호를 입력

![DesktopShare](https://lh3.googleusercontent.com/35Sap6rnEEISQsBv5t-kukIEDYvCe-CwcUBh3Bc4QVw0MnWidtFRFPwkQhW5mZ-y_7nAgyBpmz6TPzVLTPucfD7TO80XOvES8UaRBUUKcmJAHSokFFA6SzO-_0QGZho_7MCFGseUOSNax-cfCQt5gK28aAGNHgzRlu1Qwp9YlhC6WaMydocI_OE2dFqrZdVKKxQm-yemlpdJmYBr4KmXG4Y37REMbVGqy_ck0F2f0FTU3qmBK2w0NNug-_031XfPwEGFuBTWrwlb7vC_YrCmCcAv2_mEwunPnzhGMnXxA1onaBTxZ7iHA14zTWL3qzuSszwaUqVe_N6LDoL4QbpM8pwBIfQUGNFd-XsTwhuIo9BrbWdnadIoOQLPnFKkS0ar_gzGpNW-6sBQYo9SRjzJdj5mr4FKoPp6NXqhSaCZIA8oJQMEwKccltFvG-sYshMZTDfrRjoteejcJ2qMhapUGf_LzV7wsC_nOrQEYICZ3nwvjBNKmdi3YFNaCXORMjhSBE8OoHmM5ohx_iDRmV6C9pyb97cp_fqRLBLpIQXweZ6Bofyt3AWNtdfPLHUDYTROITQzaVKgAU9a4YeO4v9q74KzQW_BSxCJXEK6CRs0GJM1vHpi=w440-h443-no)  

이렇게만 하면 될줄 알았는데 그렇진 않더군요.  

## Unckecking Require-Encryption 

```dconf-editor```를 열어주시기 바랍니다.  

~~~
$ sudo apt-get install dconf-tools
$ dconf-editor
~~~

```org > gnome > desktop > remote-access```로 들어가서 ```require-encryption``` 아이템의 체크를 해제합니다.  

![RequireEncryption](https://lh3.googleusercontent.com/PVk4wRbB8OAgb6OouP8mULi8tTXIiltULEFRwR4EyTlvyjJXnB5h7TcKBoiQzwlic7ExWLD7bSD3dDzlXlXQf_d3werVUoywa8TEZikHc1xzBUYCx4rojE7p9qyj02YYUGLW4lHI0tneaDeAncSfGLALhiEBBrsFXtOSyrzm75yXRYZfaK56ADLCUPVFbPZd3yluIwWX4fV1mbz0RKRnxYl6rGjyr0gKEzvmMq2k72f1zza74nulmKvevbdC38wvXLYr3ppyEWPGXF6iHiZ9NOoUm9qEoj7iOjBV5Ed_0280SdEBXIPk6c5t0n2kj4FTY1uqlgP1U6p0gVFX6t8ReVwG3nf3CXsxeSBADZ7AaJ1Jo2TDomu5DBIj3sKdnp05KQTJbKDVqo08E8Gap0jvgoK4tzeRIEXXxyFu4eTzS8KdC5g-bG2NmgOlyuAeBpMDR-U-kT1CbA30-E5N4yv7igOWkPFEgj97phCVtAx9eNLFgT5Bcw9KaV3tDeNLXwyZFqNrxpWNYt85B1P4raDAtOAQyFjcby8yVfQHIE0UTzKo8VKmlgColtsch5Ihh2lI_56ihX9iGW4TuBMDlE90J7-7lYSz8CSl6cJ2RC-Jn6cUSVby=w800-h628-no)  

이제 다른 디바이스에서 VNC viewer로 원격접속을 할 수 있습니다.  

근데.. ```require-encryption``` 아이템의 체크를 해제한 것이 마음에 걸립니다.  
편하긴한데 원격접속을 하면서 패스워드도 입력하고  
누군가 그 패스워드를 알게 된다면 그 사람도 원격으로 접속할 수 있다는 이야기가 되니 여러모로 찝찝합니다.  

이에 대한 해결방법은 좀 더 아래에서 설명드리겠습니다.  

# Android VNC Viewer

VNC viewer는 windows, Linux 등 다양한 OS에 맞춰서 제공됩니다.  
저의 경우는 Android에서 시도해봤습니다.  

Google Play에서 [VNC Viewer](https://play.google.com/store/apps/details?id=com.realvnc.viewer.android)를 설치, 실행합니다.  

IP, port(5900)만 등록한 후 접속하면 "Desktop Share"에서 세팅해둔 비밀번호를 입력하자마자 바로 원격접속이 가능합니다.  
Android는 Ubuntu와 동일한 wifi에 접속해있어야하며 내부IP를 입력해야합니다.  

암호화 되지 않은 접속이라는 경고를 만나게 된다는 것은 역시나 찝찝합니다.  

![Warning](https://lh3.googleusercontent.com/XKJ5AmnInMH6jtwyFGysIWEQNpCtPo3xYcXxDN2cyzJVTo9ZJYluKWFOVHsdsQB1vf9x_I5fdWiki-C7gGRdwmL7Ndl_QQVduQvWEScF3CvubczzqJ3PfWw_TxpYMgajJja4HjgEmNPM6Cf9ox1ZMgVfRAiEFMHVF3eqMX1JNmgXao9Fp2QCVBxTf41yJKvNsVV-3_ZpCVbvw62mpa81w6FZD8zgaWL8um7ukcUExR9MxRmVwYkf_xgxVVOYzWblWn-mFBJd9r4Q8hmO0BOpZjTLaWm9YpCmlNYbtr9hZIosI3I1LBvpu5kt0EQkyyVLNg3jxC39Xh0ZXfLiTUhYyafv_NNqhZObHq5pOXIK2Jsru-GqV7qy-IWhpUR4ZQ6yZMEpK4EYMORJvh0dy8fivK-sic40A4252keRICD4icnUDN4EB17EiYmKcrgZQCFCgqVpRX83Xe6o4IaEkGymIPu-ROaHeGtMt68snrjEMoKtk_K1YGSSDdggVSHkqImw3gsiAuBVmawOMVv_jE4fybCTaBnDnGsp6sZUwS1p2NFkJS-7sDp5nYnqsQnZfm27x_yxFqz0MIzYEWiBqqcUQZQ4-t3ZbGpEVOWhworwTkjLZfCi=w379-h673-no)

# SSH(Secure Shell) Tunneling

위에서 계속 남아있던 찝찝함을 해결하기 위해 SSH tunneling을 이용하기로 했습니다.  

우선 SSH port(22)로 Ubuntu 서버에 접속을 하고  
VNC를 위한 데이터를 SSH를 통해 Ubuntu 서버에세 보냅니다.  
그러면 Ubuntu 서버가 VNC port(5900)에게로 forwarding해주는 방식입니다.  

Client 디바이스부터 Ubuntu 서버까지 암호화 통신이 되고  
Ubuntu 서버부터 VNC port까지는 비암호화 통신이 됩니다.  

하지만 동일한 Ubuntu 서버내에서 일어나는 forwarding이기 때문에 결국 비암호화 구간은 없습니다.  
(설정을 잘못하면 비암호화 구간이 생길 수도 있습니다.)  

## Install SSH Server

~~~
$ sudo apt-get install openssh-server
$ sudo /etc/init.d/ssh restart
~~~

SSH의 기본 port는 22번 입니다.  

## DDNS

보통 가정집의 인터넷은 유동IP를 사용합니다.  
주기적으로 외부IP가 변하게 됩니다.  
ipTime에서는 우리에게 도메인을 1개 제공해주는데  
외부IP가 변하더라도 그 도메인을 이용하면 변경된 외부IP로 찾아 갈 수 있도록 해줍니다.  

```abc```라고 도메인명을 설정하시면 ```http://abc.iptime.org```로 접속이 가능해집니다.  

```ipTime설정 > 고급설정 > 특수기능 > DDNS 설정```에서 도메인을 등록합니다.  

## ipTime Port Forwarding

```ipTime설정 > 고급설정 > NAT/라우터 관리 > 포트포워드 설정```에서 SSH에 대한 포트포워드를 추가합니다.  

외부에서 22번 포트로 접속이 들어오면 Ubuntu 서버의 22번 포트로 접속하도록 설정합니다.  

![ipTime Port Forwarding](https://lh3.googleusercontent.com/-qFy4yzzhQOHLWaGl0kWOQbLo87HViOe4RBy3DX93_HpcRAcXxfy8gnwcpLgxn9M_gAk1XOEgrg22bLcS-XcR_NlanbiuCXJf3Nklhxpih3GDrkzpHywWQKZTaSW5ZOaohRDPtV2JTxuIFz77vbr5Ao_cThWNwigbiHW1vpehnjx7iValW0t5--D6-jcV8ucB0UHDSIs4-JH_gSKq6vRn6n47RP9XhFUTnlSkMRrz1Igyn_3ZHRlwhxAq1RoJQ13NUJu4kXIdVeM6zUNWwpPAMdaLzsC0Iad2GgdtQI76htC4y9sV_iX8kEHVwcyVmBI3LR82mYKypHt4VB6GEl0144gtfGKRwLW_saLnLS4yZQwRgLFweDfd-JeEI5GnlajiKaRaWVPDL1nxNxQh0_C4E7alHrZX36QSkLBTtnM7nuDIQ3lKbrsBuLxFCrrEdcBYhRB5WVNJ9f7GEJsu3yze0hhEQVF3Bh_mE0saH3Mt9_B8H_u1Zt6z40YU4zlXEYUsPaWc05CBP3B1NYnxJx5zvhmIWyCOwLdOLs1CNxWBTS42lYxwL-kh9tJdbDz-jliaKSFl3t0rWbH2yCo-KugDkVXRDox1wdm9M1ln-mBXCuQKRbo=w877-h405-no)

위의 "DDNS"와 "ipTime Port forwarding" 설정 덕분에  
이제 당신의 Android mobile은 집밖에서도 Ubuntu에 SSH 접속이 가능합니다.  

Google Play에서 [ConnectBot](https://play.google.com/store/apps/details?id=org.connectbot)을 설치하고 SSH 접속을 시도해보시기 바랍니다.  

  - UserName : Ubuntu에서 사용하는 계정  
  - Host : abc.iptime.org  
  - Port : 22  

SSH 접속을 하실 때는 Android의 wifi scan기능을 끄고 통신사망으로 접속하시면 집밖에서 접속이 가능하다는 것을 확인해볼 수 있습니다.  

## SSH Port Forwarding

Window에서는 putty와 같은 툴로도 위와같이 SSH 접속이 가능하며 앞으로 진행할 SSH port forwarding도 가능합니다.  

먼저 [ConnectBot](https://play.google.com/store/apps/details?id=org.connectbot) 을 통해서 SSH 접속을 하신 후 오른쪽 상단의 메뉴를 누르고 "Port Forwards"를 선택합니다.  

아래와 같이 등록합니다.  

  - Nickname : VNC (임의값)  
  - Type : Local  
  - Source port : 5902 (임의값)  
  - Destination : localhost:5900  

![SSH Port Forward](https://lh3.googleusercontent.com/9QsFHd7x3VqeU2EORq9KBm3DDWJxwB4jin68UxE4_6h4boxe2HokC1-FsB2sIT_T7a2LfT3qPeZ8vgmAwylkf6MxZqFcQDstKlcIhjtynmdEGlfwMgckHtv6KpxWoNLK15WZLer7STH2cKttHk-eSgWtU-Y-CZmOTqVnhedLdb-KsiSKaNlLrYe0ydB_vM5Qdzrvj-iqHGX7i0I3HuDz5jHl_kS4pth6NnwjkA5ymEdR4vzRpE4TIrLo3hX1l3A-WV2nqy2mSNNBSwz8cCZfGcKl9QsFxP37r71e9yVBsoQ2P92l1R5Zw_XNN17bZQ1lt_1ngIN6eHwCwKgkM50BmXHWa5r06N_QwqxJXWeGaS8u28PlASCaq4_Jpr2SZesSL4RhuMMcmLqjW055nAZWSBRiIerGRAuoogxxRxzFMbPnEgZBLebIdIwplGxyPAAIdspfr64un5jgopLxmzSH6g3XwGut2iy4v9PH6LsJS45nmxUvuT4Ys2RCIsOBejmPgyvGf8Oh2nJPry9FAvt31-FzU82watb2Dqw9EQb0o56ysnxr2JKMZ4nYUkFC-WuqHGnoBxD3pGU_5NxpILECn5QH2XuNSFfehErQuqeiDTjie8B1=w379-h673-no)

아까 설치해둔 [VNC Viewer](https://play.google.com/store/apps/details?id=com.realvnc.viewer.android)를 통해서 다시 VNC를 접속해보면 정상적으로 접속될 것입니다.  

  - IP : localhost  
  - Port : 5902 (Source port와 동일)  


ConnectBot이 source port를 열어놓고 VNC viewer가 source port로 데이터를 전송합니다.  
ConnectBot은 source port로 들어온 데이터를 SSH를 통해 Ubuntu 서버로 전달합니다.  
Ubuntu서버는 destination 설정값에 따라 localhost:5900으로 들어온 데이터를 전달합니다.  

아까와 같이 VNC 데이터가 암호화되어있지 않다는 경고메시지를 뜨지만  
통신구간이 SSH를 통해서 암호화되기 때문에 남에게 정보가 노출되는 일은 없습니다.  

[ipTime Port Forwarding](#ipTime Port Forwarding) 설정과정에서 22번 port만 포워딩했다는 것을 기억해보시면  
abcd.iptime.org 주소를 향하는 호출은 22번 port만 Ubuntu 서버로 전달되고  
22번 SSH port라는 안전한 1개의 출입구를 통해서 VNC가 동작함을 다시 확인해보실 수 있습니다.  


# 주의할 점

SSH 서버와 VNC 서버는 반드시 동일한 서버일 필요는 없습니다.  
집에 서버가 두대있다고 하면 하나에서 SSH를 받고 다른 하나로 forwarding도 가능합니다.  

그 forwarding 구간은 비암호화 구간입니다. 큰 문제는 되지 않습니다.  
다만, destination을 내부IP로 세팅해주셔야만 합니다.  
외부IP 혹은 도메인로 세팅하게 되면 forwarding 과정에서 비암호화된 데이터가 외부로 나갔다가 다시 돌아옵니다.  

SSH 서버와 VNC 서버가 동일한 서버라면, destination을 localhost나 127.0.0.1로 하시는 것을 강력하게 추천드립니다.  

아 그리고..  
좀 별개의 이야기인데...  
wifi 비밀번호와 wifi admin 비밀번호는 신경써서 관리하세요.  

