---
layout: post
title:  "홈페이지 검색 잘 되도록 만들기"
date:   2016-03-15 12:00:00 
categories: Homepage
tags: 검색 Search sitemap rss feed google daum naver 
---

![SearchConsole_Hit](https://lh3.googleusercontent.com/wrtKRI9LBH4RC9PkwybmCtmLNq5DKtZ1zbPEwwWcLs4k5fmjT0HHv11uNUHVbgc7ZczCF-fxxftD-vyJq3jhLVNVgTirfC8R5SyuB7BD8cR_oaA29vhAElCXi9F3S0R1lXAO7oL1OPLZC40tbP6NhlC4cRvaSkPENcKPQKHCXfNBLLb0J8s3vkynvUlRE0HbVYcM6vG74vGfLiG43wO2fDdqtsfxQ2OfZKfl_ezQ5FcVY8Yc-NV_yu2nQwWMz9CvUHaYr3TSooI7IgZZOHxjOekvUeJQM-qkMfea2Chjcj3BImKQx__9V_-RBy6nNE7unHoz5yrHsbka6_xlQ89SqeLPJX6HWodCWIEeuLxEkFY8YLyIvGrF6A5mOiGYH5sOEWddYC9myv-EKhYReaeuzXfc98XvQyc8pKSWMRsDRR3vTq-XNElSKL-PMtymum8SdeMLx47QpaHFslhdVqRCUVQA5cM_u5P2WM0Yg4WEBUmmDMAvPSkwZNosoPBx5Dlod_HTeU_tlQgTLtp3rkLJ9-TGcEPKG6gHVtjnHo5qBgybLwWNO_4h1FVxrEDZxKbQ2KsM=w385-h683-no)  

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
Disallow: /index.html

Sitemap: http://dveamer.github.io/sitemap.xml
~~~

특정 검색엔진만 허용하기 위해서는 User-agent에 검색엔진 명을 넣으면 됩니다.  
저는 Github-Pages 를 이용하기 때문에 별 생각없이 모든 검색엔진으로 세팅했지만  
개인적으로 작은서버를 돌리시는 분은 고민이 필요하실 수도 있습니다.  

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



