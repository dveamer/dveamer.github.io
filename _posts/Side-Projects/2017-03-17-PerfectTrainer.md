---
layout: post
title:  "Perfect Trainer for PokemonGo 포켓몬고"
date: 2017-03-17 12:00:00
categories: Side-Projects
tags: PerfectTrainer PokemonGo 포켓몬고 Android
---

![Perfect Trainer](https://lh3.googleusercontent.com/rPCJSqWXRd-fIrxikGOieUTRk9x0F_HAgyCAUdRDtOMi38_bMXq4ht1xa00rGGrsjWbhNgqEQGHMLoA2TWDEM7SbO5-_pQj9VevKjSAYZ8YQOKErRMa1qhFFhGHomNSk9nEwPYYEfwwwqcHp3elyQwej7jjkKcD6mPtSgex3tlotnVuu76ac59b6LvsBTgn-hsALTxnuUjMHmSgiwVwznluVykb-CIIbi7EmMeAz0SLlmXDFJqZqL82SVOCuuKhVpCaPolLVnrOLEUHM6jGlBXmIcbbxY5BXc2v7jqFZEWepOQ-gh602VVz4T6heyh6vZORD72rnO6dbvv8GE9jKNFtLe6gieQv0JAwqz3qXdD0I7WpAhQhJY7_JsK23QzWwphVrsi4TPbBcucjXuYM8SoB-LGLE8ttvwGmbKUp1JjZcGX7Kzw_rraR0nOCMJDerlCHI12BlPZhFWX_fhlswwtQsrte7QVfc-w_yVtQgWRVJFyWfX65AaHIIPKYL2LgIiXcZaFornlZwHLtC3fXTm8MJg0u44edNslK4CtGLmE0QnKhtgKqSwfhxh0Nl3m-bempUWdpOvt7zexs95DGEUMGrJB32mJhd11Rxe_OMCdH9g2GQ=s512-no){:class="imgTitle"}
![망나뇽](https://lh3.googleusercontent.com/q2wpz6ZmxN57TnCTWZu0U_gZqVZWVJyzwpy-ZIX1Prf2MlE84EoGQj9HKVTuiO-whZVmPazsHbWxJVBCDA1tZbACj9vAoWCCeFXiuJA3JyHLdGNnkzGt_tkKgnAjpzFpg0_klsyDX4F06UlayYXb3tyCWpxcfRsUxaGt0CixYXcd2jsOmDD-PJn1PsnxGXJ_E_ijSi_i7jcdoBjtDrl9XTH9w10jSrw5OMyJDQkTBySmAFbXXcloXZOIGa_LywLh9LiVtpSHcLoynQ4hhjJa7P_PlE-YpoH4s1SsU1UXHD-XT6qtujl0YWHr4559t7mZiPzCP1kToaFpQP0O5Z6jvm6vAzkTkC_8gAUKKzt4YvCuwjRbMvvXOZ_4NQSb83N7ax8AXWsMNOg9RXKv7btyinrOYYrDayOhfjV1cIUym8JNaLeNbwsORSkBn7f1zwn8hZZd4EcMmmggMzV3VwAf5c0W956OaRp0eXj7gTPDUqxDZNkFqM2ud5_WDRHPZ-1I2tko46o77JCISnPAVdJKQRh-gKBkoX0sdLzJgNzSiFqcfiDBYJdbbqgb6YOxvjsXpQ5ITuh0bsb7ewt6b4KcoVtwbLU4LVEq2_LCi9eqMPzz36d8R0qSUZk4L8Iuhm8p4Dhlhc0BPcVvYEH6f04YpZ_AbvWLdmElfMqzrHOqyg=w1052-h1015-no){:class="imgTitle"}  


[Perfect Trainer](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer)라는 Android 앱을 만들었습니다.  

포켓몬고(PokemonGo) 사용자들을 위한 앱으로  
앱을 설치만 해두면 주변에 전설급 포켓몬이 나타나면 실시간으로 알려줍니다.  

개발과정에 대한 설명보다는  
앱 소개와 Google Play에 초기버전을 배포하고 난 뒤에 겪은 일들을 기록해봅니다.  

<!--more-->

# 무슨 앱이지?

## 포켓몬고는 너무 빡세

[Pokemon Go](http://www.pokemongo.com/)를 2~3일 하다보니  
"시간 투자를 엄청나게 해야하는 게임이구나.." 라는 생각이 들었습니다.  

트레이너의 레벨 올리는데도 시간이 엄청나게 들어가고  
갸라도스 같은 강력한 포켓몬으로 진화시키려면 잉어킹을 100마리 이상은 잡아야 되다니..  
그리고 잉어킹이 아무대서나 나오는 것도 아니고  
선유도 같은 곳을 가야 많이 만날 수 있으니 직장인인 나는 주말을 투자해야한다는 결론이 나왔습니다.  

게다가 갸라도스를 만들었으면 이제 미뇽을 잡아서 망나뇽도 만들어야하고.. ㅋ  
"끝이 없겠구나.." 라는 생각이 들었죠.  

근데 우연히 사무실 근처에서 망나뇽을 잡는 일이 생겼고  
"아.. 굳이 한마리 한마리 잡아나갈 필요는 없겠네" 라는 생각이 들더군요.  

## Perfect Trainer 가 되어보자

[Perfect Trainer](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer)를 설치하시고  
앱은 꺼둔채로 일상생활을 하시면 됩니다.  

아시나요?  
망나뇽, 잠만보 같은 전설급 포켓몬이  
우리 주변에 굉장히 많이 나타났다가 사라지고 있다는 사실을..?!  

맨날 포켓몬고만 들여다 볼 순 없으니 우리는 그 사실 조차 알 수가 없습니다.

일상에 집중하세요. 하지만 전설은 놓치지 마세요.  

<br>

예를들어  
주변에 망나뇽이 나타나면 진동과 푸쉬로 알림을 줍니다.  
알림을 받으신 후 [Perfect Trainer](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer) 앱을 켜면  
망나뇽의 IV, 나와의 거리, 남은시간을 확인 할 수 있습니다.  
그리고 OPEN_MAP 버튼을 누르면 자신(파란표시)과 망나뇽(빨간표시)의 위치를 구글맵에서 확인할 수 있습니다.  

이제 두 다리로 달려가서 포켓몬고를 켜고 망나뇽을 잡으시기만 하면 됩니다.  

![망나뇽 발견](https://lh3.googleusercontent.com/tf6BGULKuFvi_PjZBgc3lIzlb1m-2hqeKKf_e4AkMkUHikLa2BcudDPsu4jGD0mip0qCayc40EG4ngXmd8PgBvj2NnW1GUCnn0qSQFDY5XQxzNG1BZ30M2QZblJdlYy2HXcN5WF_VanF_NuIBkZdAl3zfdKbtiZyrqDoHs-rX-OQnalO-c8TsAI-uYic6rn-MziEHUO3f7Vo-DlYScvubbiT7hnS8i2HONT5CqqVzrG_l1I9rQQZ0f41XdRTzIICaytDYEsjcoIJPrOSlxfoVgTOeR5O2fFC12lZmrA-0KiZxNzXMxfu6xUDi4Uw1tIswKR7mt7ZeQmGYcpAUH4ZuaKhxIFuA4ueSetiUyZPU5RRObvJxWtlMNn2vWUjwdcoHtqtidZGB8w26OrSPQrcG7kNQ-L9R9HLV7u9ezfuQ4efs1rvZ1uE98Y8Vo7Qz3q5EIz74ZlvRVLBKyJ2Zw8fffKtyOE98-wtIDiQ1FQ1oUfV-ydM8424SShzTmdnABBEHvtre_p1grJZdNHFcCxAGohIMnq0hAtNKWgseg9Xr6BjvYFIWLZvSOaxBNlBxHc-iGuxTLVIup8C0fbajRutJdCaZIAEGrCwV31oEcrx9GLc1maNtR75=w571-h1016-no){:class="imgSmall"} 
![망나뇽 위치](https://lh3.googleusercontent.com/NxPzvh7qTrxl8Sp3ijU3h4YRz2kifqnkBGpB-b1dlBOnCeuButIyCkkTphN4YNG_Qcb7IKAfjtGcOjPVCm2v_dzk0u7H-iHINNsrMNK8MNqZ0ITuFmpHxVdN-mwBI3q8X7e35XRgmzwXER9rSNgGX55gj-hxt2MwFNs3Zdy8Y4A9GZNq1kWA4Tge93OZ_jtNApiAJ_pDd1NLooOG9kxnPNVWtCQZ-HgyUlalZtv21bOW-iMR5hQ2me4K85LBlJ_BhxH--8cie6Z0DoQ9e5bMLhWAIM3LmRlH740M7ht0kirgeoDbOIhgZdEtkbjqT24O5sEJO7M8_fVdZhSXeFqHj24tvEVDYF2h894sK6vPzx5McvCpDETf4CvhQh4505tAvZm4QqHn5ujZujDIAfH4CGAbE0hKWt3aiNtjKEYXx4tSfFHrDpEFwOd2NkHQdPimrAcfOYq8_GxtLQx_5i7rK-5LsUwCJmRKQJr9c_SXhoDyBJ9bzxtOvX82PZP9A8aiD46Q0CTeObzZ1lbi_-h7L2canfUO8kaPfvzMbrmXqp3j9odz-9Kl-_M5qwOEAxMLVetGGo7hkxtleWS2aiZcg5p1wMsBaKIFUpnDsdLAS83PMvbE=w571-h1016-no){:class="imgSmall"}  

<br>

평소에는 일상에 집중하다가  
알림이 오면 잠깐 달려나가서 전설급 포켓몬을 잡기만 하면 됩니다.  

일도 가끔씩 쉬어가면서 할 필요가 있습니다. 잠깐 굳은 어깨, 붓는 다리를 풀겸 10분 산책하세요 ㅋ  

일상을 놓치지 않으면서도 [퍼펙트 트레이너](https://play.google.com/store/apps/details?id=com.dveamer.perfecttrainer)가 되실수 있습니다.  

### 사용법 

![Wanted Page](https://lh3.googleusercontent.com/vWohSLSIfhxmShbfmKYzXxDc7PVI5lXg4vcnYOTiE_zRVcct7LZu6vNA1bjg1VCG2XllLpT6oko-kQz0ybaXmJ63kz1FbD4POlz2tdUqkjAO0titFZKSq4MT5Z42EgzTMUoLKiGzTzB_C9zloVCxazVBMHwNPscY8SZ1wqNJS1ssecpTesvBM8uy_Y98HvWMSbT-MeJcGTPyA8IU5sXypI2g4Ep6GxTQoUVQqptO7RwP4_Lwo5Z_hrB_3ZjfydtZ_bvjPcvd_8vDzPZjgAcBK7SyTeTA55aQRgLgeNiWoxCV2gsCqBgCFRbhPBlvUFR74aQaL992Yh8Iy9OWFyOrNr95cU8pcn-ZzeuLBBt1ngb2iYgn_9znIzv2wZ38vxruO5rnvCdmMtxHXn2slh7p8SH2vtm6BMykK8xCnz3rVPnu_0AzOxzlFkSxqJo0WMvIJMeMArqbPCi4J1H1L9LELfZZa9d9IeKhqGO7I47LHawBC080I7-2vQqzIIkS0KkebrLTt2hBFIAsXL2GN4HKIas_wtP9X0qN4uCaJpElvg5Z2t-A5bXAmtK2t5zTfCzvyCSHzfp8EKIMSEERbNVTGz9NoTTulNRZlLRhY7W2htLB_cgZLqAG=w571-h1016-no){:class="imgSmall"} 
![포켓몬 선택 Page](https://lh3.googleusercontent.com/5U7TxWnbCToIG4PEewaJvC9Wi_n5uKuY6PF0m6ZtK-0taHbCzfmB_h0hpbFeupkoYuSvoo6uwtjx5siJLJydvLpfYQvd7umVfNyfy1VBzNzIVKp9MCar3vpu3-qwSN5Xx33967_7naTOMMwsKPszMTyrFJBegURxtgeV3-3lW6GbcWgCfhM7mO9iJuxep-4P90hef5QCjxb9-Pbv5MrD2YZaAuAURUp6T_4EbHlW2_BudKi3alpEg33TL4ylq01t5lsbpIdWdf42d2UZXSJQj4xrkqVEJoUZJ1e6VVYBzn9JP836tfmIMxnHoQ8CrIcuM_sXkNpsrBHUkToE85MEyZ1D-H_Hg3C8-el-KYsSK1rtfsi5yKbGHRlZKnZfKCWJxxGnDjY793GX2aPp67EMYXfeK0t467zRhcntNpp92CpEmnSevfOjHSb5ufiX1T82H5BoVmBopnip5h-2AiU-v_WorG7ayxMFBdMFrxOx7MfGHz4inQ5BzRpfemrBir-P49VA_o51uLkJzalHSonuKizGJMnzMHVCtNT1QVRTKVDdRCqAH9YIQT8VEkLkW7J3aDqF-QOXr5mDiDQnJJU5335CyUqa9Q4YXkcv63UIotdJhCdu=w571-h1016-no){:class="imgSmall"}  

"Wanted" 페이지에서 오른쪽 아래 +표시의 동그라미를 누르면 "포켓몬 선택" 페이지로 이동합니다.  
"포켓몬 선택" 페이지는 잡고싶은 전설급 포켓몬을 선택할 수 있는 페이지입니다.  
원하는 포켓몬을 터치하시고 알림받고 싶은 거리, IV 정보를 입력 후에 SAVE 해주시면 됩니다.  

선택한 포켓몬들은 "Wanted" 페이지에 보여집니다.  
앱을 설치하면 처음부터 "Wanted" 페이지에 7개 정도의 몬스터가 등록되어있을 텐데  
그건 제가 임의로 등록해둔 겁니다. 지우셔도 되고 더 추가하셔도 되요 ^^  

# 포켓몬고 팁

## 상암동

다니는 회사의 사무실이 상암동에 있습니다.  
은근히 상암동은 망나뇽이 자주 나오는 지역입니다.  

집 근처에서도 망나뇽에 대한 알림을 받은 적이 많지만  
상암동 만큼은 아닌 것 같아요 ㅎ  

## 갸라도스는 선유도

갸라도스는 지금까지 알림을 딱 한번 받았습니다.  

아마도 한강 근처에서나 자주 나타날 것으로 예상되는데  
제 생활패턴상 한강은 주말에 잠깐 가는 것 뿐이니까요 ㅎ  

알림을 받은 그 한번도 선유도에서 받았답니다.  
다만 강 건너편에서 발견되서 포기했었다는..ㅜㅡ  

<br>

그래도 갸라도스는 선유도로 가면 그나마 쉽게 만들 수 있습니다.  

잉어킹 구슬 400개를 모아야하지만 선유도가 잉어킹 밭이거든요 ㅋ  
게다가 포켓스탑이 5개가 모여있어서 볼도 계속 공급 받을 수 있고요.  

가보시면 수 많은 사람들이 포켓몬고를 하고 있어서  
모든 포켓스탑에 항상 루어모듈이 돌아가고 있을 거예요 ㅋ  

![선유도](https://lh3.googleusercontent.com/4NOrwKRlGWU8W4xuebQpNmcQTUHiv_zb69ywpe6p1qj7ryzO5gmhkHcA7EebI1h1CqOPD8uwYYOahdNTo-q5wDH_w_adG9u4QrW5gR-VvwzkjAptEx3AHirSrLuOtHdmTC2mJ4AidsXKRpcO8Vqb8lMNu1BtM2RZ51ROwJbMeVFk0SVTh4HBFJH1N3vMbIgpixS54VKQIbGHiYRMfwqlYqXI1f3MDH4TmXJLo4VwGkal7Ai-5ggwv9r7QcdABDf_eg1Uufs-bSZWgUj_mN_Slnl2x_dr7J6_B6sOOv76ce9TCQ47J8FZGFSAdG4V37atwpXhxJmBUC2WbHl1X80XcwibC7qCLDAplgDG9fRdprlslx3KVCFnH3NZGdRg48QRtlMDOtWVxg_Joak5dnxVgDTF6fZnUGuvR5C00J_per7fZN1Z_vI0HeiKSwhmySro-riJ87j67_MsxxZrQlBgAJxP5r6BVSlnZH1eMrnuZKDphp_hFUX6py0mmoVWaHNu1_Zgxq5NUMrywc7e_2Sf3OGMBzNy7XDoEqiDrgcZ7jw_AhZRQrU5RQ7dcnlck3vB-na6IK5MeDVny6rrcC5HQDQUpKr2gc6h_T1m66W_GjG_cXip=w571-h1016-no){:class="imgSmall"}  

<br>

제가 잡은 S급 잉어킹과 진화시켜서 만든 갸라도스입니다 ㅋ

![잉어킹](https://lh3.googleusercontent.com/YfE-eXmWWm11dWgZViwPNvl2eopLx90W5OZm0hy0lm-FUOlue9EDdF81scZXRrcs5MMMTYxw6N3QVdSXpKKI6mYiPv4yOF5eMogMa7rUMmrf0kaV3gVFbrcjR8j4i8INb5gFkx-uc-4b7pNnUQuEdAMp8tCAcyYJSTglBamyoavgq_Sc0xyI2dj0RqhIa_4_bpoj1tC1KGGUADNUjJbovCGUdeU5qzY4I6W8V-fyCCG6QxjMrGJrjmqU_-Dtg7sXlEiN2hfJ3_oEH2eawC_RkTKSrZJRe-q6NC0HpiI4xGih5-4faYUPZnLpAC5_B0JJMjnBLDaezIZFmTP38-IjVkTe-nu7Kae5d4ZFkCkoRqeq7sfhblDZ3pDyzVJqDlj3zuSQpokxiE2zfxN-eLM8g8HsMrznbLXSVwYTiwQuudy85MDvSeGUEgaL078fMUrQTgmeJzBinjk3nIofeAuLy156jks2iZNSz4xjSHiFw1Lvw3fvxJucLGS7F6HOBomkXBSMRyRFA3oeElhHs3i5WY3mXz1ZFeJ13Vvyqx2VpCn4fVefbTldd2lnlfxyNmAgQnJqHz9hjvCEFyiBP6X2EZtLWuQMZWA8-SuZ6rP6WJEoZ4O9nwEc=w571-h1016-no){:class="imgSmall"}
![갸라도스](https://lh3.googleusercontent.com/XBmdrqJj6NiM9ZxMFdaL_8MnEP3b0XpDkrhIpCGeEVNilWfYuR-b7NkwqUZiVbEziboPqKLcKaIz_Yq9HP0dB47olDwz9Es79oO-ho_U1I07_hlCeO-vZ-_itZIrEP9qwyjqxEvXQdIxfZ1TgtdgAE0FwyuNOUfXQ4rR5j04MA7ITksJhyi28h2ZBrLR29Zfek7PtvuyNmgyukcgPL_NuupPI-XqQx_CQcKvVZTuMvvL_0eh_K2653CxaIJ9TUA3iVllUXbfz4eeDwpD6_oPRT8RbUiCp390CRhZ8ZlAHU2Ikib4ocMTGzMA14ZlC_-qwRyi4pf-lOtolyV69qD_OH_2sOwK1chpx5pXrwI1mBK1t6oETT2PrFHATr9By7rf2ymumURnGxeclHph80G97gl_-cackITWOqYBOoAlBHjYxcyV0hHVg9FguCa4nhkz-qEXFwUVz9MXu-pHca947rgSlFTzzp-FandRS2irnYBS_CHg26PLvIpdbvAy1bm9_wha1LQDn44LNb4qp1Aa6jpUy5QpWfADSVdKSzQdJbtoiD2KOYlypmfzCFxt4R-Gu5Z4lEufZACiaKXZGt_9CLImq0s2MzohgK0VHdVfTkS5tYuz=w571-h1016-no){:class="imgSmall"}  

## 어린이대공원

![어린이 대공원](https://lh3.googleusercontent.com/fQSiQA5P0XaAHIpidwLA-_aAFeruedGnOVKmPOucl3kPFVEpv6hf6GhyxgV1qxePGoqUvJ1Dy4jp4wnxDm8mCZkmPQBa7fI86kqCoZmiED7UjjoFSX71JVAxPO8jjnG8atJNlJj7h9JYACNHVB2lnxCQ4IrS6PuotncGuwkGdJW2pExhvOoyV-s0LfEIR2Vw6pbmwNzcgqBfU-4UvfE7IthqwguFgt5lUSbBYqybcnvOZPf8sZPJlMfenoZA-LUHVvJm0nNhaYaq0CqhCaG7aU8R2PXzW7zcTyjESdVUaxeD1dN90M6pSeW86yw5shwdXdLk5tthv9rF3KdDBkJwwWiwhDG9eCzLLSEuINqO73rWCNckX_WDVjYyRb9QssFVeI5z7notqhxVNJDURnIbM5f2IO_fNCHLA5effTOQvYCmOTGwFCf3qGduTOzRwG_WgXGs4YZ-qAbuZkW7BaQt8Qrzx6qjoZiO1xZldBE6ECZS7dBsSvgU1OcAQipAoQqFTSUCe10WJcsUzMZir_WAo7Ejv2Wm2dz6b6xfai47OKnDfHOy4x3gwB9C2LRRvh1ZY60gadC-2I0SH4Lof4T7WU4T2iJOgvUFrm_fIuopjucRKZT8=w571-h1016-no){:class="imgSmall"}  

어린이 대공원 옆을 지나가다가 포켓몬고를 켜봤습니다.  
어린이 대공원 안에 포켓스탑의 개수가 엄청 나더군요 ㅋ  

끝없는 스탑과 몬스터들이 있을테니 레벨업 하기 좋은 곳 같습니다 ㅋ  

## 행복의 알

"행복의 알"을 쓰면 30분간 경험치를 2배 줍니다.  

그래서 평소에 진화시키지 말고 몬스터 구슬을 모으고 모아서 "행복의 알"을 쓴 후에 한꺼번에 진화시킵니다 ㅋ  
진화할 때 경험치를 대략 500xp 받는데 2배를 받으니 1,000xp을 받게 되는거죠.  

그리고 진화 된 몬스터가 잡은 적이 없던 거라면  
도감에 새로 등록되면서 500xp를 주는데 이것도 2배되서 1,000xp를 받게 됩니다.  

하나 더..  
매일 포켓스탑을 돌리고 몬스터를 잡으면  
7일 째 되는날 포켓스탑 돌리면 대략 2,500xp, 몬스터를 잡으면 2,500xp 준다는 것을 아시나요?  

7일째 되는 날 행복의 알을 쓴 채로 포켓스탑을 돌리고 몬스터를 잡으면  
5,000xp의 두배인 10,000xp를 받을 수 있습니다.  

지난 번에 행복의 알을 쓴 채로 포켓스탑 돌리고 몬스터 1마리 잡고 대략 80개정도 진화시켰더니 85,000xp를 받았습니다.  
레벨 21이 되자마자 22 되더군요 ㅋ  

아.. 참고로 몬스터가 250마리 정도 꽉차면 진화시킬 몇마리만 놔두고 전부 박사에게 보내고 구슬만 모으면 됩니다.  
저는 250마리 잡고 정리하고 250마리 잡고 정리하고.. 이렇게 두번 정리한 다음에 행복의 알을 썼습니다.

## 퍼펙트 트레이너

![Perfect Trainer](https://lh3.googleusercontent.com/rPCJSqWXRd-fIrxikGOieUTRk9x0F_HAgyCAUdRDtOMi38_bMXq4ht1xa00rGGrsjWbhNgqEQGHMLoA2TWDEM7SbO5-_pQj9VevKjSAYZ8YQOKErRMa1qhFFhGHomNSk9nEwPYYEfwwwqcHp3elyQwej7jjkKcD6mPtSgex3tlotnVuu76ac59b6LvsBTgn-hsALTxnuUjMHmSgiwVwznluVykb-CIIbi7EmMeAz0SLlmXDFJqZqL82SVOCuuKhVpCaPolLVnrOLEUHM6jGlBXmIcbbxY5BXc2v7jqFZEWepOQ-gh602VVz4T6heyh6vZORD72rnO6dbvv8GE9jKNFtLe6gieQv0JAwqz3qXdD0I7WpAhQhJY7_JsK23QzWwphVrsi4TPbBcucjXuYM8SoB-LGLE8ttvwGmbKUp1JjZcGX7Kzw_rraR0nOCMJDerlCHI12BlPZhFWX_fhlswwtQsrte7QVfc-w_yVtQgWRVJFyWfX65AaHIIPKYL2LgIiXcZaFornlZwHLtC3fXTm8MJg0u44edNslK4CtGLmE0QnKhtgKqSwfhxh0Nl3m-bempUWdpOvt7zexs95DGEUMGrJB32mJhd11Rxe_OMCdH9g2GQ=s512-no){:class="imgTitle"}  

더 이상의 설명은 생략합니다. 설치하세요 ㅋ  

[Google Play](https://play.google.com)에서 "Perfect Trainer", "퍼펙트 트레이너" 로 검색하셔도 되고  
"망나뇽", "잠만보"로 검색하셔도 상위에 노출됩니다 ㅋ  

아직은 "포켓몬고", "PokemonGo" 로 검색하시면 스크롤 엄청 내려야해요...  

잘 검색 될 수 있도록 별점 좀 부탁드립니다 ㅋ  

혹시 앱이 너무 좋아서 친구들에게 설치하라고 추천해 주실 때는  
"망나뇽" 이라는 검색어가 제일 편할듯합니다 ㅋ  


![망나뇽](https://lh3.googleusercontent.com/q2wpz6ZmxN57TnCTWZu0U_gZqVZWVJyzwpy-ZIX1Prf2MlE84EoGQj9HKVTuiO-whZVmPazsHbWxJVBCDA1tZbACj9vAoWCCeFXiuJA3JyHLdGNnkzGt_tkKgnAjpzFpg0_klsyDX4F06UlayYXb3tyCWpxcfRsUxaGt0CixYXcd2jsOmDD-PJn1PsnxGXJ_E_ijSi_i7jcdoBjtDrl9XTH9w10jSrw5OMyJDQkTBySmAFbXXcloXZOIGa_LywLh9LiVtpSHcLoynQ4hhjJa7P_PlE-YpoH4s1SsU1UXHD-XT6qtujl0YWHr4559t7mZiPzCP1kToaFpQP0O5Z6jvm6vAzkTkC_8gAUKKzt4YvCuwjRbMvvXOZ_4NQSb83N7ax8AXWsMNOg9RXKv7btyinrOYYrDayOhfjV1cIUym8JNaLeNbwsORSkBn7f1zwn8hZZd4EcMmmggMzV3VwAf5c0W956OaRp0eXj7gTPDUqxDZNkFqM2ud5_WDRHPZ-1I2tko46o77JCISnPAVdJKQRh-gKBkoX0sdLzJgNzSiFqcfiDBYJdbbqgb6YOxvjsXpQ5ITuh0bsb7ewt6b4KcoVtwbLU4LVEq2_LCi9eqMPzz36d8R0qSUZk4L8Iuhm8p4Dhlhc0BPcVvYEH6f04YpZ_AbvWLdmElfMqzrHOqyg=w1052-h1015-no){:class="imgTitle"}  

제가 잡은 망나뇽 6마리입니다. CP는 낮습니다.  
IV 80%이상이 두개있는데 강화를 시킬까하다가 S급을 기다리고 있는 중이예요 ㅋ  
잡으러 갔는데 망나뇽이 도망가는 경우가 많아서..ㅜㅡ 조만간 9마리 꽉 채우면 다시 올릴게요 ㅋ  

# 앱 배포 후 경험

## 앱 정지 당하다..

앱을 정식으로 배포하고 난 다음날..  
Google Play에서 앱을 정지시켰다는 메일을 받았었습니다.  

세가지 이유가 있었는데  
첫번째는 앱 이름을 "Perfect Trainer for 포켓몬고" 로 했었는데 포켓몬고 앱의 이름을 사칭했다는 이유였고  
두번째는 앱 logo가 포켓몬고 logo와 너무 유사하다는 이유였고  
세번째는 앱 내에 포켓몬 이미지들이 저작권 문제가 될 수 있다는 이유였습니다.  


그래서 앱 이름에서 "for 포켓몬고" 를 빼고
앱 logo도 바꾸고.. 앱 내의 포켓몬 이미지들도 다 제거했습니다.  


이게 처음에 사용했던 로고입니다. 돈 주고 산거였는데..ㅜㅡ  

![Perfect Trainer](https://lh3.googleusercontent.com/A7M1Zr0QNQtog8mVMqHcGTOFo998fXzGP8Yu9dok6UPEYK837SbtHH_nmbpCy-Fa7gfuzGgusJuNqoE0yKUfe5gtEIG0cyRkNhmd3eo2erchyqcDkD-1xAySnbymn0hJzIoHxLTF0_6lURpMOgpCjqOmT_hD3Xneuty3pGsG4HQsqioZ_Lpu8Itr-P5pNyI12FIfJdLCLeT-g69lOvmAulwQef-2Ul7Cv2r2aD_nimXAA1CL0kGN6VlZx9G5YYzxswnwksMc_HKlAyF9UkNrjlxVqXTN6ruzL51f5LinN2F0uCR5JA4DC8pW6c2xhlVY-mHQRiwxvpC5VqyylELl7K41JYlCkXTbMsKcCxNe543xHia9w4uJ_9bCh1N7_keoJiROqTN3nmKJwjusWkDy1vu__CP9SZzosnBa-fVLB15fc42zi0QsYVqYQcsIiL-pg1iDu38V_fZmjHXTl-xghcs-xUXnDEImxo8XcOFWsMyIaUdDQ3tjlGZLTirjU8SSiSYGGPp-SbPI8e6wcOMVuh60GAZV4XcnyjJ2hus04fpPG6wztudTCfSHmw9KqPkCcUSaSiXp1-fnzsIO8_HMNS6D7quA45ZCORqntyIld06fTe7R=w170-h169-no){:class="imgSmall"}  


그리고 이미지 지우기 전의 앱 화면입니다.  

![Wanted2](https://lh3.googleusercontent.com/QOb0RcFOSPxKMEWKqs3BruQeG-xTnk4BWvDCAitWuxspT_Qs-H4zGGe07rZXib4TmYKowkhZ04nK9495E8Jzo5edbUZNSPRe-M0OgXToSiA4JDBmJh4lXyCCstwplHIRFGBxxWl1wK-_CaUuIUjxyvidbhNCGhZqyGwcB-s9E3-Hf06CSSiUMsvAb1tk8cLhDw_ZKIw9bsXZh5UNMvbZY4YbCszGRxesx3UFQFyoCnl7f6ZZZAZkyrFHVwjBU1rD3HSbqRkAEd1LP24E2t14tgHTFTei48W5nks7w2mHwYchr_mDOYVW6zJj5Uj6Zr491IuMazTR1cG8p93drMsXcfdvgeG6UrvBH54R9-sbHaDJ3VUfc5m0dQC2SBVNEAFzyt2v9W1hQTx1XzssZW0Mq73s1lDqo0tdDSom9WvAAzf1ZJdc3F-A0rjkTcrY-zW0_DoOV9ZfPKu7Y1cxXnpJvKX3LwFYH9SjRIT_JVgletgVfKt94S8oeje-CMfwz79rTm-xbGp6Nc_a7vJdH1J-EFpuSK2KHUfGQy6vKjpq4Y5CiHR6KaqEDAZfrQKgGrn7TgShkWmgbUEOEs6i9b_USyvmVe1aFJISlppcHhY-GoifNVYKJ2sqeU4ovhyMIFIHsJm5oJ7t4H_NCNFdwyBR9G1dlyOJ1U3PTxaqvR0DwA=w571-h1016-no){:class="imgSmall"}
![포켓몬 선택2](https://lh3.googleusercontent.com/HK0rlp84iP940uhlCFxhojPruzJdpVdntf_DeBebh1_WM0Qvcc6Bua3_7hT-BWHkXL_rJfc82PsRwNYWKIwu5jdT_f6KZpugWQmhSnu7XSgvK1S-VUsJhlYv4O7_gVNQIM40OHvJeIIxonRGC2tXmkofBnrWihp4WT2lqKOR6oUUGgchEMPOpg6nMfaz7ZUh0B18oKzli9MtS15aHyuPeBwrm407rxYx5WSgi5-AplX52rdApL71IoVIIBjnO2XUJTT5ypoGAEyDh0GYCPv2eDy2VzTwvnXBEyB7AkiLOJ_QTOmVB6J84Iyr7Td1r3331ifGypNACbdj_jaKRbsWvhiFBgkLSWlbgphq4CvMt0hKilMtvOe9YKpp47Q83npH2YfTDax4qQrFZN71nwmton5hlHlNbuJ-h1445xi2eIG4UBLSMobTBvShecpaB6AksTfDbnEBxcX9mroydr4rilCAi_d5nwxmRnhYGmU3aUAGcHgv6G5UR6-vLs-S3VVzXVvsVfzV4NfuOnACIurlMaDN9QJkPUDjC_kEl7GQNwYKUY_mV5UQZsI4UsiHznPkaJ4mAN6wiU3cApLQJef7Braa5gA800Ou88Gd83mxsZcbreer05B2zwv7meqcFTK9l6YYahNFMMBwrDFRjUL0FFpEkmO_lH-zJQetpQVKJA=w571-h1016-no){:class="imgSmall"}  

<br>

경고 없이 바로 정지 됩니다. Play Google에서 검색이 안되고 구글 콘솔에서 프로젝트를 열어보지 못합니다.  
그리고 그 다음날인가부터 광고도 출력 안되는 것 같더라고요 ㅋ  

맨 처음에는 앱 이름에 문제가 있다는 연락만 와서 앱 이름만 바꾸면 되겠구나 했는데  
구글 콘솔에 못들어가니까 앱 이름도 바꿀수가 없습니다.  

구글 플레이에 앱 이름 바꿀테니 정지를 풀어달라고 요청을 했습니다.  

그랬더니 앱 이름만 문제있는게 아니고 logo, 이미지 저작권 문제가 있다고 추가적인 메일이 왔습니다.  
그 대신 콘솔에서 프로젝트를 열어 볼 수 있고 변경 가능하게 해주더군요.  

아마도 1차적으로 정지시킨 것은 bot이 한 것 같고  
개발자가 항의를 하면 사람이 확인을 하고 답을 주는 것 같습니다.  

정지 풀어달라고 요청하고 답변이 오는데 2일정도 걸렸고  
logo 만들고 앱에서 이미지 제거한 후 apk를 다시 올렸더니 하루 정도 걸려서 정지가 풀렸습니다.  

거의 4~5일정도 정지상태로 있었던 것 같네요 ㅎ  

## 앱 홍보

### 선유도 

선유도를 가려고 집을 나서려는 순간..  
갑자기 "앱 홍보를 해야겠다" 라는 생각이 들어서  
간단한 홍보물을 만들고 PC방가서 인쇄하고 문방구가서 10장 복사를 했습니다.. 헉헉..  

![홍보1](https://lh3.googleusercontent.com/K-v1zxvw0Amn921pLpjoWRlnpyCWD1KiLQONoz_x5mHdsMpgV9rb4wPhKF89k-8nTjj-i_Xgf-V3nuanOFCWS6O83A-A3AbUIjwp9sg0wjEVMsXKa4QGwW6Uf3ijO3kQPy6ofy98HOqmZNvwgiOcPaRl7uqwu2IXd_qJx3Lg3cKLQsVt1hajsaTxHbIC4qsYw-2xPGHj5XDY9mBkrAr01FwmdNr0Cc8aLfJbP04_XI6ZZBfbq128uI9vydRzlK8rAB2jje_QjR7O6ytfZ32GPY6a7hwCeHPiSxK-l5Vobr-8qXylQJgHJce9OnMZbaaIZY0Ct7TrHNlW9mR-blq57lUnxDioVQ4VJsFpMX4gsfRrCS4E1jI35DkV6_HinnjprJ8OxnrtODaPGfFQ_QiHjdxihwVeD9F7Xym8rtb3AXd1p5omIptGb_xiy4Xe4xgivjxltHcShmyHnsy-js43pQeVvGSzPbiGt_keS0kMl6vGvntRnELUQ5bv1OAWZRF3XF0Npihi-hpTyGXA2HVIFy5hCBeDEhIkQtGt09W1NAcsEcxN6RJmjWX4aV21L7Jp5LECOH63jniPJ9VF6cJSvlD63BgdedGcZZvTcvcSml7tKZcGafdfuOVqZsT8qELTd6MtFuoK1T_kz-2V1O7_S0qd1jsg2RSmVxR_JopUbw=w1665-h950-no)

그리고 선유도에가서 포켓스탑 근처에 붙여놨다는....;;;  
물론 2~3시간 후에 다 떼고 왔습니다. 저는 문화시민이 되길 위해 노력하니까요 ㅋ  
  
그렇게 총 3번정도 선유도에서 홍보했었네요.  

### Blind

Blind 라는 직장인 익명 앱이 있습니다.  

거기에 두 번 홍보글을 올렸네요. 원래는 한 번만 올리려고했는데..  
올린 다음날 탄핵이라는 희소식과 함께 제 홍보글이 저~~ 아래로 깔려버려서.. 몇일 뒤 다시 올렸다는.. ㅋ

## 베터리 이슈

앱을 배포하고 초창기에는 베터리 소모가 굉장히 심했습니다.  

처음에 개발할때 위치정보를 읽어오는 모듈을 잘못 이해하고 만든게 원인이었죠.  
근데 원인을 다른 모듈로 짚고 큰 효과없는 베터리 최적화 작업을 일주일 넘게 했던 것 같습니다.  

그 결과 앱은 계속 베터리 최적화라고 업데이트 되는데  
일주일 넘게 베터리 광탈 현상은 유지되었죠.... OTL  

당시에 제 지인들의 소개나  
선유도에서 홍보물을 보고 설치하셨던 분들은..  
베터리 광탈에 깜짝 놀랐을 것 같아요 ㅋ  

만약 이 글을 보신다면 죄송하다고 말씀드리고 싶네요.  

지금은 원인을 찾아서 해결했습니다.  
아주 만족하실 정도로 적은 양의 베터리를 사용할 겁니다 ^^  


