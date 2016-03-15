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

[DAUM 검색등록](https://register.search.daum.net/index.daum) 에 접속합니다.

로그인 후  
"등록" 탭에서 "블로그 RSS등록" 을 선택하고 "블로그 URL" 과 "블로그RSS URL" 을 입력 후 확인버튼을 클릭합니다. 

![daum rss feed](https://lh3.googleusercontent.com/8KiXI9yrt3jCl-eJOthd2HeER2zn3mw-j-wDrXQ35LTHvGG1qKf88Oz2g6KwpmKISjGGMVkyeswwDcilucwyUfN7oWUJym8kCoKmT1KJQ9sMi4NWrpFov0nnqz7cf6PzzhBEx1sLcVT2-zmYEFoMjJ_5aGntnXJSMqJWpWWWEI2wduxYy6SY6UJy00RZQ0UoeuG1FGPrhJBC6KeOPJ8bCooPh2rI4hsJTcYrlzeFSrhYWcOqODggT2CpoSDvFb5CGCuSG-n-ct8aZfsKd29yiSXDPq59JcQ0B4WGZ8gUjMQIns5O7e4mr3r-7EXJKg9h7Na7ov-YCDvI1OsxJ9y4nEa54SNaMnE1vMH8iwH9WD2P-2fv0Xz4C9ACwO9AHkSVTT8oCHI8tyiv4_a6JUmLFra-jLp3w41OSRUvJ6Cxcjx396Tuiqxax9x6h1I_ggKeH6A5xs9eiZSU0sq7Rga3gf1uQLBybJyiVLR3l3QkPJNQt4ofp_WKVhEUQrrZwKngUUknDWmwI1XA0DVevsb8G_E6IrDs4dXMYXvjCnpy2RhkStD_AmC_8rcpMkHE3IPZnYTL=w1310-h683-no)

# Naver 등록

[네이버 웹마스터 도구](http://webmastertool.naver.com/) 에 접속합니다.

일단 로그인을 하고 사이트를 등록합니다. 그 과정에서 "사이트 소유 확인" 이라는 과정이 있습니다.  
소유를 확인하면 Google Analystics 와 비슷한 서비스를 받을 것으로 예상됩니다.  
저는 "사이트 소유 확인 불가" 를 선택했습니다.  
그래도 RSS 등록 가능합니다.  

![naver registration](https://lh3.googleusercontent.com/XCiN7VXRiwvNymLRY0UVbosLHS2-yO-Mq79tn5HUj6ER5cKiIA0W8Fc4XbKmfnDnd_mj63hy7-he00jNdNaAn86Gb4cHV3N3zsFf2vGWjyFATjXirAC7KPAZWMpNcJU-x9urvzH0mXSvpd7qbIkwWvcYKrwj4X9SaK-NMi5uDnu9hjCGWd8k0UvyGY3cz7XEzlMl0bS8h75zid_E2cs1GXW298Ox1JznZSozkeNBSFS9Ec9z4dBgURrNBFF0giX5xT-gRn-sYegxX87PGerGtflqdgW1bHHTdEuQ15mOJFmuyvqHK7YZoHzGDieHGDM3H04QKpaFZ34x7rIrje-gPa_ZvBsA-A6nECXL60j6UtmZx-9EXNm4MXwgjk2yxryBO3JFaiHZnBBHyUV5564crhCzdTrOBbkcVfVl5TAaH6P_vZpoX3Uo3FjK0hl1MV7XKpQW8Vi5TqXmaiIFVwObw_QhEwkIX1mUoPYbrEey6DD4XsSiX9BZmMYst0nVlAQ-SOTMb8P9uPbdGdqEOZGox9Ks0vmwE9B2PyZCaF6GmHGYkImjJlYHI0Rc0tn6oZZ81vSR=w1319-h677-no)


추가 된 사이트를 클릭하시면 해당사이트의 정보가 뜹니다.  
왼쪽 메뉴에서 요청 > RSS제출 을 클릭합니다.  
도메인을 포함한 URL 을 입력하시고 확인 버튼을 클릭합니다.  

![naver rss feed](https://lh3.googleusercontent.com/8bYfpfCGRpa98MfPevx9RwbsV1KydI764rzSsxcWvXGlSVbPCAz8AWcJEZeY5MQf8QiFGYPxnehafAikHOzyHJ4p9N4UlerjDa5tTFW9RZeWT0-M8JJwmygFkiORxY1uGWWSTv__dGSkSSeKSaMpWNphIKkg6OcaGmTzBB11aYH7YMTNqkNNBR7QG7iwdEpaxT_hRFqxhwxHXaK9yAENMixNRhf3_jqADaahYHVwx_rPQkPBvEZwUgZsb_TeDuuMPrFGc2-Bt9DTmBu27Jw2-i9jNL50bJ5pTTT4nwEZOHjd7UtccHJwRT370Hz_4JXlYY50-FWWc8-I08lPH7VuoZ5T_8RVeTs0lNhybv7a0PuBtm6T1gj1Ek6sQAgz8Ncm_S0P8YSFAPBG5NOSZpLLtiuJSJmrJ9Ldb8O4Ye-RdjyCq5HZU9YK0OncHw08W1vXaxBPije2BPqFhProe0uLZQnXJzHFvfiEY1G2tz2he5cSC2hw1WZ8CzbsvVbb2WrDZPWhq_I_zcdTb6RrgmtbTy6nHsyPUZ7yRtZihk6cWRHXvffNutU5H_VR1ZXkDlBnyjSs=w1325-h680-no)



