---
layout: post
title:  "홈페이지 검색 잘 되도록 만들기"
date:   2016-03-15 12:00:00 
categories: Homepage
tags: 검색 Search sitemap rss feed google daum naver 
lastmod : 2016-05-14 12:00:00
---

![SearchConsole_Hit](https://lh3.googleusercontent.com/eqO7RM8OqBeq9nOskYhXC-pjjq4Lg5ESyqX5W-2WM6hmIDyT085eUbSkgE_BxKF425f2wwsXFsoTQTBdaiP0BHUUdc9-0n7t9lesHvdXmhomX7Uh3Irt8az906EkbkiL3mpQrp_T91WiXfTkRyzwG9n1YBdF3cXx2vT0SrSsszrILKe24CYnRtU0oAlKUwuOlPhfXHaPKhb6Cx4Ttfi4QHiZA57UVhXWZSivlZ55G2Su8JwGmLWSVhjb4Sun_GDPH97Fdy9VsUxCF_TVr2cs-cSFUne0Px-Ej6U2HRccEvsllZkGVuFHHuPMW2HJleVjs5rC2MTGcQQeMET-egwHQhfqxifLRBxHyCyZDt-rmSD0J4vh9yVwtyab9ukysjU0LThBZeTImF8Kf9pqdwGzNkrvZ-EM21vsHidu6eDEA3Bbw0mWbisR4h0r3cZl_ehk4JgZbmSKlKp8qvHIHGVbUpPKp639JI3KlJIyLUoNc8v3E_eNpuKnEVrNkzoEnUr1D-nAQfpkFhHv1IuuccgMrAfM0UjjlcwwcRZ2RBCWbU7d1VzVsfZdTnWleZvGPI1wadrtCt4K2umYDExaGQdW-BI88MhzTFY=w373-h662-no){:class="imgTitle"}  


목적에 따라 다르겠지만  
그래도 방문자가 있어야 홈페이지를 관리하는 재미가 있죠.  

네이버 블로그 같은 경우는 글만 써도 방문자들이 있는데  
개인 홈페이지는 그렇지 않습니다. 만드는 것도 쉽지 않은데 서럽네요.  

위 사진은 Google 검색을 통한 3월의 페이지 방문수입니다.  

<!--more-->

계속 3명정도의 방문수를 기록하다가  
Google Search Console에 sitemap 을 등록하자마자 11명으로 오른 것이 확인됩니다.  
고작 하루에 11명이냐고요? 제게는 많은 발전입니다.. ㅜㅡ  

홈페이지가 검색이 잘되도록 하려면 일단 Sitemap이나 RSS 가 필요하고  
Googgle, Daum, Naver 같은 검색엔진에 등록을 해야합니다.  

그 방법에 대해서 알아보도록 하겠습니다.  

# 준비하기
  * [Sitemap 만들기](/homepage/Sitemap.html)
  * [RSS Feed 만들기](/homepage/RSS-Feed.html)

# Robots.txt 생성

robots.txt 파일에 sitemap 파일 위치를 등록해두면  
검색엔진의 크롤러들이 홈페이지를 크롤링하는데 도움을 주게 됩니다.  

/robots.txt 파일을 만들고 아래와 같이 입력합니다. 반드시 root 위치에 파일을 만드세요.  

~~~
User-agent: *
Allow: /

Sitemap: http://dveamer.github.io/sitemap.xml
~~~

참고로 
특정 검색엔진만 허용하기 위해서는 User-agent에 검색엔진 명을 넣으면 됩니다.  
저는 Github-Pages 를 이용하기 때문에 별 생각없이 모든 검색엔진으로 세팅했지만  
개인적으로 작은서버를 돌리시는 분은 고민이 필요하실 수도 있습니다.  

예를들어 구글, 다음, 네이버, 줌만 허용하려면 아래와 같이 하시면 됩니다.  

~~~
User-agent: Googlebot
User-agent: DAUMOA
User-agent: NaverBot
User-agent: ZumBot
Allow: /

Sitemap: http://dveamer.github.io/sitemap.xml
~~~

만약 크롤링 되길 원치 않는 페이지가 있다면 robots.txt파일에 ```Disallow: /pagename.html``` 을 추가해주시면 됩니다.  
예를들어 http:dveamer.gihub.io/diary/ 아래의 모든 페이지의 크롤링을 원치 않는다면 아래와 같이 하면 됩니다.  

~~~
User-agent: *
Allow: /
User-agent: *
Disallow: /diary/

Sitemap: http://dveamer.github.io/sitemap.xml
~~~

베드봇 차단하고 싶다면 아래와 같이 하면 됩니다.
 
~~~
User-agent: *
Allow: /
User-agent: BadBot
Disallow: /

Sitemap: http://dveamer.github.io/sitemap.xml
~~~

## List of User-Agents
 * [List of User-Agents](http://www.user-agents.org/) 
 * [User Agent 정리](http://www.mimul.com/pebble/default/2007/11/30/1196425260000.html)

# Google Search Console 등록

Sitemap 을 등록하게 됩니다.  

[Google Search Console](https://www.google.com/webmasters/) 에 접속합니다.  
홈페이지에 해당되는 Console을 오픈 후 ```크롤링 > Sitemaps``` 메뉴를 오픈합니다.  
오른쪽 상단의 SITEMAP 추가 버튼을 누른 후 만들어두었던 sitemap.xml 파일 주소를 입력 후 제출 합니다.  

제출 완료 후 화면을 새로고침 하면 sitemap.xml 파일이 등록된 것을 확인 할 수 있으며  
색인이 접수 중인 것을 확인 할 수 있습니다.  

![SearchConsole_sample](https://lh3.googleusercontent.com/0QRzIfkzEXW8msaVZ08-7ekvqyxrc1KmH2ci78ZuFHHuIJmsajXCNUdPDJguqVWLPOVhBl5Jbu4FdfL8oPXPk_0ZONudVlV0TNzid-MtMF7rZp00YD-B6-snvL8K4JDtYfkkOIWZyOZPTg1TeyQ5vsuCE-xMORBIqtguRByfv8FYy00yJih3R-KfletZJzZ-KgUso9iiUZzTMhOJPFBIHkksJvSfugrywMclAednFn0l4Q5wyJ7uRBuiBwLJixyz69AmXwMw2MMSaMhCQvUii2ANGEymV0fHuCvIXbLzkEQ5lSoSqIeQpzT1rdAvyxwwtVPcONMNtEaAmgbT5XWLfPr26LX5WXvWdEFLUwcM0lIk_Pf8GM9ncrIYJMyM94lounWpiemmtDL9fqHK4Re5v5wmZK0axtnQ81yrOKLES3ZMezRBGdGTQwBRtfYk8WkPEJ0NJr1jurSyJ7WJX_mhkviXwp_2vK31udPP_1wurBWdI1Rk6zg6sxGlGTEeBzJl6N8S_YdCPKgty8YIM25lE0b2Y93Zgdwyz300H-eLElVg0x6y84i5OveAXbMvUMt_UqDe=w1280-h683-no)


시간이 지나서 색인이 처리가 되면 구글링으로 검색되어집니다.  
제 경우는 "접수중" 표시가 사라질 때까지 만 2일 걸린 것 같습니다.  


# Daum 등록

[DAUM 검색등록](https://register.search.daum.net/index.daum) 에 접속 후 로그인합니다.  

"등록" 탭에서 "블로그 RSS등록" 을 선택하고 "블로그 URL" 과 "블로그RSS URL" 을 입력 후 확인버튼을 클릭합니다.  

![daum rss feed](https://lh3.googleusercontent.com/deODbVJv7cAdTAoNpTSeNPoJMvC4SqCu8s1L0fyI0c-aczcFimH7HiJaUrgLrsqJg9dbx9L0vjfhijlhqUV0Lnqoje3UG4J5ZRSi85V5aZ-o-cEPOKwUKQNNVUXLENQ-k6Euxr_YotDuxa5oIrYzQXbBjHr--GKoZr23Y6HpQA21ZLvLBrZiHSBySpqwgJkT6cuXSDMDtsPbX0mRHDpu-IOCdDqV0vWZoWKWVhpoO6ESRpnStZO570GAQTUjNvWcvPFEupUtZfau5usgKvzK6RXFg7gex56KMz7aOlehSR7tCAGm16FI_ayQyzn4Z2Z3whXD7bUDSTTwQ9HaScpByoIQB18rmyOT9u63qzG73vsVgQQEbDbqCzOQyyOV31CUiRCkcsDcSxObWFWM12vxxU9LO_awkc-aANhdabEcT3n99P7q4aQhk1R5f4kMS0MgfFq95z-14aJg5oUSl9jlJRCOHLXr9floIJshoFqv69RFXcfugnQiZZeaC7efax4kK9ZXKD0x3pge-JiKRdbxWoDb-pymQL7TSCyljqWeGIwpDMG35dc237CI85O36IaErZza=w1306-h683-no)

# Naver 등록

[네이버 웹마스터 도구](http://webmastertool.naver.com/) 에 접속합니다.  

일단 로그인을 하고 사이트를 등록합니다. 그 과정에서 "사이트 소유 확인" 이라는 과정이 있습니다.  
소유를 확인하면 Google Analystics 와 비슷한 서비스를 받을 것으로 예상됩니다.  
저는 "사이트 소유 확인 불가" 를 선택했습니다.  
그래도 RSS 등록 가능합니다.  

![naver registration](https://lh3.googleusercontent.com/Em3umdygMeVO658B8jsYBDfNGNGR3vk2jfe8k4vr01b5aR4CGw_KLHRBIZ7STAtDcBOm6OtMF6oMGBdklb2BgR_NoWmiW_wiU1ZzyeXqFfPELpPoskXcejfm1OwhDyZBQnNF9_L2OKufocZR3H0Lt6MUCucd7rCMCPEY5mRlU7y7EAcRkhLFZSYDM7JBcmfF9Ox9wPU8uG5ocQQCN1AYBYkbASCdX-VJX61ac16kMWAuPfub97pQnnaFGhBgL6SkhKKvm1iOrQlS_lI7qZafXHHRQM9aEjymcbJC4-8SzJUsJePLboSgayYLcV5Xa3ApL_Hl9IzGI8TLmvto_3GDIOBbsiT9tZDaJ7vykFDeWaEROKuIxby5G-QYAG6u6bFCMzrkU1LxPJN_par9UlPTz1ZNkCPwFupz7rVRHp9FsEH3QlLQ5czcsmKYm3PDa1lIRmhIqyOYtyMZFhZ2gqocM6lpQ_IfrioX8CJlVwh974Ae66X7uLuoqSziUBVFvuIZqyp3GpM7JJwsGh6dFRwFrQctA6FINtytbpBQQhGrTfD3pGuPtra2d8s5T-vYr1vo1HCr=w1306-h670-no)  

추가 된 사이트를 클릭하시면 해당사이트의 정보가 뜹니다.  
왼쪽 메뉴에서 요청 > RSS제출 을 클릭합니다.  
도메인을 포함한 URL 을 입력하시고 확인 버튼을 클릭합니다.  

![naver rss feed](https://lh3.googleusercontent.com/8R12rpShMt_2SRiW7IzxERkiRi41n0K6X-GmWhQmDR-HA6NFTFauduh0Th2DNQjPt_zSqPrg7hmtHmkkRP_aWmvn3LU7OfkBpw5LYg5ggSK00KczfwdD2irjWXwOhvD2nUhdPu2PlelBIrpFaI6dIesupSCDOuvqZi8o6REgTz6H86TadYUg_KXDRxYI-dkF92mb8kGJCxjUQD2IwtIMBMKnGk_bCUmf8K_R1SRJTlbQsFHN-eFHAwetqVUG6mTHr0NNQYmRv-tGVdgZyVrI3HWQmPDiqTji_wTbl_9D052eVH1IJYvfaRqipawapWchyiyvw895r6T6bz5zQ75O0QKz1VK_QrfC6AumDBg4pWFVtkFceJBYgs0G-xRnlT5mkOeHxksm8ADE1Wtx-tZHzQO-jcs-Ui4ywjtrdWJsa5XHDgWWMvut-8e-vmoWiT4xMgPdqsWL-3aYuWBFg-G4AT-OFhNqPKHAzKIrYhRcaozuqK15nXcbI9mrqNN91sb1ULMwN09lTzCMLj965HAaZP9Is87GkIGLWO6pMb9N9d6wGd8B12VOvrgy52kr4d1HnEFM=w1303-h663-no)  


# 검색 결과 

첫 등록을 하고 2개월 정도 지난 시점에서 검색을 해봤습니다.  
Daum 을 제외하고 Google, Naver 에서는 검색이 잘 되더군요.  
[Google Analytics](https://www.google.com/analytics/) 로 모니터링을 하고 있는데  
Daum 을 통한 접근이 다른 곳에 비해 현저히 적다는 것은 알고 있었지만  
Daum 사용자 수가 적어서 그런가보다 하고 있었는데.. 그게 아니었습니다.  

평소 방문자가 없는 글은 검색해도 저 조차 찾을 수 없을 것 같기에..  
평소 방문자가 꾸준히 있는 글을 검색해봤습니다.  

근데 생각보다 상위에 검색이 되서 놀랬습니다.  

대중적인 주제가 아니고, 제 글의 제목에 포함된 단어들로만 검색을 했으니 당연한 결과일 수도 있습니다.  

## Google
  * 검색어 : Log4j 2 설정  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 8번째  
![Log4j 2 설정](https://lh3.googleusercontent.com/WyartDp23J7WA1YzhDqK8NnSN1IR9SJPtjlqKrtr64ieAdz9MG5FVGinrzxJXkR0_h1jtffGFFl4I2HAqmlyVTOHDOtxwdqqxPV0QaoQiiLlugOeuDhEC1F4MHZEnOTFXDB1CzEMpBI5eSUt6KPZQ0CJlmjvqiSwUwWdu-Vzrh7f5vnmRCMCosyeTJTs_2voCyicO5v2YMBPlO0gdPcTWbEj3fNnNY2hgmqQlAPIgnICGy77ekQayOmyRVilyLf_wQL5neP2VS8xJLzw22m7m-vnaXTBgxtsRcHOUv2eDUsXtUmmMe96sZ_gtAdbpkDeVVOb4PresnDoMxDZMAjHxXWpIYTRYhhsbAcXqvndgp64QqsuV5Ro7J7UiB8Y65LdBcIgMIdPHSZM4ZcgHK7kYlc7e28Zrkq5wxQm1lHaLsBM1S1W931Bbw5Rw8PZYKOR3fwUG5Dev_DdKL7JcWP3nsz2QL8xIilAa-Hx-5WKZSDQEKlGfAiU7Xhny6IfUVjTv05WDJA5SIY7FTrnb97cbDFfzpAzbAanMA65X_Ss2tvQQ3IChnmYp8wxcI_tAD5Dw8BDsRfpGGZMaltAFxB49OIYT47slUw=w490-h662-no)


  * 검색어 : haproxy keepalived  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 2 페이지 끝에서 2번째  
![HAProxy Keepalived](https://lh3.googleusercontent.com/zzy31aGGuVZmELRT1loHIeWNReQpDqDycbg2HESnGLbEzGQlPif4U62Uk6I2DlLAI32yNbFfuBeCUKa61Zg9GXupAIYXeMC0zeQKd0BuqvsePxgii59zrWzoKs5wUiKU0poixoa8X2IgCj07Pv8IzES3punZkc1JjFIXsbYhyYSYe7EqzwVtrIWAOLTN7y2qURC0NP0M6uB74dvP-YH-ZkM5Rib6hsuZgSlkBt3qU2HB2b3mDUowBCDlSag-mVO_R8Hu1XW95UlaVHBFtf5gCZ95yGiD2N1xqmU1yOytZ_FZSf4ATClzpls1nr5YEhOAsgDY2_c1BAJzxvd8i0DEI2DGGhlqV31z2jw_Z2iuV_NjpiySuIL7mbFNXM8Zqo66ezfDoULy0C-XPBWhJ_wvEFPT7DzxHNS93fzIa57Jx3LQGPUMJsZAfWqk6wDTVcZKevPRBwD7VISeQ4ngA5sjmxwHG7wYmM2UUsCQc6qVEPWt4WB60O3ZSkQpSX859xP-RgOXpb0Xvg3wfmn7qz8H0d2aol1RWFhurxzneAYtmlueLTKYMyt256yzeiVtGVpTU0INZWyQOfR0bqrDBpNl2UGuqHLwG1Y=w708-h662-no)

## Daum
  * 검색어 : Log4j 2 설정  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 1번째  
![Log4j 2 설정](https://lh3.googleusercontent.com/I9TCrPGAvnflr2Xr0_7zI9eJpXTy9Ul-EU8fe_8c1ONR2Dsa4FDq8r5r21OaJ6nf3_XiwZygp38LB1JdXm3T0h2mZ1gFXKfwTbDGn6ElJrgQ5Z3WAm31OFAsIhq3DrZKhY6QK-lmzx9OM25fd2ebG4rlm30HbienLox-QQBqYbHsrmk04EuNEuDtXx5ndYplx6SygiBvLx94bePfCiEKdRg_lSFBxdVGKOgiUgmtCbqoMca4QoDVDbRt43o4CcRekfJ53Y4TuwfySDvQKQvvFsiKTMomcBh3Bm8N-AHaT-o4GbojcnqOL1FoPXaXmOVipXCIuhV0DqGPQv6AriIrZa6ZtqfodaRDgyEpNI2dov7BpJKyyLUXTQfZgsQ_WgJRfXuyJiQx8ThuIw5PJgynfrZM0TGixZ8Gma37oeDPadYleBTmRGbAoXFSH1QsImoCaJ1Rl_ERiRv5MxxfIEAp4Q2uLWwGP8nH7bTF69T25fURjP6nWqjapYl7WLC9evNoUFdwI9ArN3ZeoNJR-7p-o9Wkw4j1bmsWs-t2Kn_gNijOjJQnGr4eMMZETLPcUkB8Xopl_5EOG19iTTHAUGt6LeM6y6so3RI=w783-h662-no)


  * 검색어 : haproxy keepalived  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 검색 안됨  

검색어를 "haproxy keepalived dveamer" 로 해봐도 검색이 안됩니다.  
dveamer 로 검색해보니 딱 2개의 글만 검색이 되더군요. 

검색안되는 이유는 두가지 정도로 추측해보고 있습니다.  

첫번째 추측은,  
글을 작성한 시점이 Daum 에 RSS 를 등록한 시점보다 더 과거인데  
Daum 에서 수집하는 로직 과정에서 과거 글을 수집하지 않는 것이 아닐까? 합니다.  
최근변경일자(lastmod)를 최근으로 세팅해줘야 수집이 되려나.. 추측해보고 있습니다.  

"설마.. 그럴까.." 라고 생각이 들 정도로 엉뚱한 추측이지만  
일단 검색이 되는 글 2개는 모두 lastmod 가 세팅되어있습니다. 좀 설득력이 있죠? ㅋ  
그러나 lastmod 가 세팅된 글이 2개밖에 없는 것은 아니기 때문에.. 다시 설득력이 떨어집니다.  

두번째 추측은,  
robot.txt 파일에 구글, 다음, 네이버, 줌의 봇에게만 접근을 허용하도록 설정했는데  
Daum User-agent 값이 잘 못들어간 것이 아닌가 추측중입니다.  

2개 글이 검색되고 있다는 점에서 좀 설득력이 떨어지긴 합니다.  

일단 robot.txt 를 전체 허용으로 수정하고 지켜본 후  
변화가 없으면 lastmod 를 [HAProxy & Keepalived](/architecture/HAProxyAndKeepalived.html) 글에도 추가해보려고 합니다.  


## Naver
  * 검색어 : Log4j 2 설정  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 1번째  
![Log4j 2 설정](https://lh3.googleusercontent.com/hZ8TqypgzELFD5ybmnE5JE97-18_aNvD4yY4kxBVvHNDm8KhlKxHt_v2sqwwrddirfB2fLi-uRidcT6yvpvcGMH4ZLRzGAnJ_n8Z7K_CI2w8hLsFtuikf-z2Tb1EGTqLhBw4GNperwSHSxUO6wgl2efIfW9cFL3TcT5QNn0svTMdUV-ovi3rNl3qJ1bLmx5sacYlo5Kbg6GY84e75nBRB8m06wWqo2Zd2IpraAe-EBPKvjOgq-Whw8lQ5m2fjr6sPVWHGoPr6SAV7h5YBXEKrxA-zi7olRaZ7Tp25DAM9C-gM-XfeOTHiG5gCQWEKxnycH0X9LpZ53S7tOlmCRic5M_PasW19YsZIbN_3ykCBxiWZhNOo56K4xXOG9D-erPZ82Opl1BE_riZaJ9qrqcZgU1nnlCpfqAYkww_e8znPLWpxeA6pgWC34CPArY-8-a9C7qIY4-WFc0UZtm8V6_tUBZ1aec3b3k_fNEexPDEerWI-CVFW363QbRK505sSG7KQo62EB-Ts0ySo28VNaXlZCMUHITw0JGeGlNj-UeR4F3ZL4Sd4utJ55kaDsJ2wpNTz5UzVGhZXk6ziTbkVejcVuBKRMpJk1M=w680-h662-no)



  * 검색어 : haproxy keepalived  
    검색시점 : 2016년 5월 14일  
    검색 결과 : 3번째  
![HAProxy Keepalived](https://lh3.googleusercontent.com/iW2aHQmLRakrmBbXbg8Cq40xrnMPoBFSaAJ72jNyNbpzhkhjjCvQdnG_FvSe1cMV1wIC7Xa4s_i4i6T_VP-ZJr2hhhGiwUWKpG4pJeTFQFckENJ_ddt3u6WCNzTECGz4K7nEQrC4TUAnHsLH-eYwSRscCKjE-yPxkc8_zsx6Iohcw0OZjW_D6TfjjWLqQzUzUpvDhioVryBPGMhRwwq1GyLuYCYlgo33Uh1sLAY3RGXmgJNw_t75Y-ClusJmKaurzdv049XNU0a3UnkT382tcBFqY-hcLijrXHXbOGsAJdqAazuwxwFGgsje4_qD_c0BktMiW1OXIKFlRsrms6vsuEzC1gSfbTJzoYC0Y6f-w6GSVNK3TPe21geCktxzEqmLvHEWgkNGnNZ3FMsr8Z5I8xwNqjEuKdod6KEaVRZ6eiF0TBvKWuBiGI8yGY1VTAzjSrTYr0vSZNyT98lpKHBFbmoTao3bQzlxRVeocG-DYgMBGCvYLI27DKozxmW5Me5RghvI_lh8Qn0MPLilAw_E7ygrLCJ_8TKgP-CVJ7_qGdT9XgUczvrKLwhBR4H0vKcsPsg18ZUOY9Myd2UA2gpaM1R3rKyVtEw=w680-h662-no)




