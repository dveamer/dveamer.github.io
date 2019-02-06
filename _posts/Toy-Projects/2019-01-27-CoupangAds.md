---
layout: post
title: "Coupang Ads 개발기 - 쿠팡 파트너스 광고를 Google AdSense 처럼 만들기"
date: 2019-01-27 00:00:00
categories: ToyProject
tags: ToyProject 개발기
---

![Coupang](https://image12.coupangcdn.com/image/coupang/common/logo_coupang_w350.png){:class="imgTitle"}   
( 이미지 출처 : [Coupang](https://www.coupang.com) )  


일반 블로그와 네이버 블로그를 위한 Coupang Ads by Dveamer 개발 후기입니다.  

Coupang Ads by Dveamer는 쿠팡파트너스 광고를 블로그에 진열하는데 겪게 되는 어려움들을 해결해주는 서비스 입니다.  

정적인 쿠팡파트너스 광고를 마치 구글 애드센스(Google AdSense)처럼 주기적으로 변경해주고 구매 가능성이 높은 다양한 상품들을 노출시켜줍니다. 게다가 구글 애드센스 만큼이나 설정이 간단합니다.  

<!--more-->

아래 홈페이지에서 더 자세한 소개와 사용법 등을 확인하실 수 있습니다.

  * [Coupang Ads by Dveamer](https://sda.dveamer.com/index.html) 
  * [네이버 블로그 전용 Coupang Ads](https://sda.dveamer.com/guide_naver_blog.html)

# 쿠팡 파트너스 

트위터에서 팔로잉 중인 @Outsideris님의 트윗을 통해서 1월 11일에 쿠팡 파트너스의 존재를 알게됐습니다.  

> 오! 쿠팡 파트너스 승인되었다.  
> 이제 파일 정리 글 쓰고 쿠팡에 휴지통 광고 같은 걸 연결하면 되는건가!!  
> 출처 : [@Outsideris 님의 "쿠팡 파트너스 승인" 트윗](https://twitter.com/Outsideris/status/1083697252541943808)  

이 때는 "파일 정리 글"과 "휴지통 광고"의 연관관계를 잘 이해하지 못했지만 핵인싸인 @Outsideris님이 한다고하니 왠지모르게 나도 한번 해보고 싶어져서 블로그에 적용을 시작했고 곧 "파일 정리 글"과 "휴지통 광고"의 연관관계를 알게 되었습니다.  

## 블로그용 태그

[쿠팡 파트너스](https://partners.coupang.com/)에 가입을하고 승인이 되니 생각보다 쉽게 광고링크를 만들 수 있었고 이미지가 포함된 블로그용 태그도 만들 수 있었습니다. 블로그용 태그가 너무 얇길래 7개정도 합쳐서 아래 이미지와 같이 블로그에 올렸더니 보기에 나쁘지는 않았습니다.  

<br/>

![최초형태](https://lh3.googleusercontent.com/g1jDO6S42USaA9NTX-J2rz4A4SdzgWb4Ft02SCyEAki6BMUk9-Jjb4M9Or7--yk5I5ewLJXGmvgEu0dGop2rSWEwB6dYdEHBeNQlFKnH5Eweuxz0TDtzpL3UDRUNZuSpCVDhi3DLCMfquysO7dtjiPjsTLhtbAyLMzcCEOgt4dc17Ab5NAyHgoQ4iuHK3_8VQoURgNHwjmmkYQSpLQVUYyX3DFr_0gN185SxTI-bPiIcNwIfTinkmjDfGBpH4oGH_f6PHUwuJHlas0otZlLZxqPSnyjNZ7MNEmIeYGkwRW21mtr14Bwqv0VgVUSyN5QVQtVQx7q_32gGgPIivW9T7xgStCPDlEzfxAsw_FoX6ONmeqCq2dMWYmD90PdrKXp_ikg7ppqu0gnvtOofltzbp8Yxkfw_ibixsspRAexm0rQcnMPzW6509UKJMdkoskbR1saB3q1gT9LVCz4L6i-IjqLPJfQvXR8pYvAzEmBkv2dFbe-cr7ETD86Xv7moZDYxIta6JHE6fcxruyUt3W68DqyHVPKLpBy9l-RE57eDenCR6_4lfeg7PJXb-m0-VxmG4ptCVCL8VwbVbnSVvkoKL4CIJo-cT0568EDnU3RZtpmtYiwQKwyjB9LeB_NdJqYCN8NI1AQDyWrlkoAJf5wjVQf_JAs2nBSuHSxmt0529T4AyKiiF_GhVFBtVDdLW9O56_a5r-52WZ1lM0x1rM_bZb5O=w878-h246-no)

<br/>

게다가 처음에는 꽤나 재미있었습니다. 예전부터 구글 애드센스는 맨날 "프로그래밍 입문 교육" 광고만 띄워주는데 효과는 별로 없어보였거든요. 만약에 "기계식 키보드" 같은 상품을 진열하면 사람들이 광클하진 않을까? 라는 생각을 예전부터 해왔었기 때문에 일단 괜찮아보이는 기계식 키보드 태그들을 열심히 만들었습니다.  

"맨날 기계식 키보드만 띄워두면 효과가 있으려나..?" 라는 생각이 들어서 50개정도의 여러 상품 태그를 숨겨놓고 javascript로 랜덤하게 7개 정도 띄워주도록 간단하게 만들었습니다.  

근데 상품 50개를 고르는 과정에서 시간이 엄청나게 필요하더군요. 게다가 골라놓고 봐도 다른 사이트와 비교해서 가격 경쟁력이 있는지도 확인하기가 쉽지 않고요. 상품을 몇개씩 찾아갈 수록 제가 왠지 쿠팡 직원이 할 일을 대신하고 있는 듯한 느낌도 들었습니다.  

## 블로그에 부적합

쿠팡파트너스는 블로그를 위한 배너형태의 광고수단은 아닌 것 같습니다.  

[https://coupa.ng/bguX7J](https://coupa.ng/bguX7J) 와 같은 형태의 광고링크는 1개의 상품에 연결되고 해당 URL을 통해 쿠팡에 접속해서 상품 구매가 발생해야 수익으로 이어집니다.  

특정 상품의 사용후기를 정성껏 작성했다면 해당 상품의 광고링크를 만들어서 홍보하면 효과가 있을 겁니다. 하지만 블로그의 글은 검색을 통해서 장기적으로 노출 됩니다. 만약 광고링크에서 연결된 상품이 가격이 변경되었거나 상품이 없어진다 하더라도 글 작성자가 알아채기는 쉽지 않습니다. 블로그와 달리 빠르고 짧게 소비되는 SNS에서는 나름 효과가 있을 것 같습니다.  

블로그 글을 위한 광고상품을 찾거나 광고상품을 위한 글을 쓰는 것은 너무나도 많은 노력을 요구하는 일입니다. 블로깅이라는 것이 결국 제 자신을 위한 것이긴하지만 그래도 직장 다니면서 한다는 것이 쉬운 일은 아닌데 광고를 위해 시간을 투자해야한다는 것은 저는 감당할 자신이 없었습니다.  

쿠팡 파트너스를 블로그에서 사용하려면 구글 애드센스와 같은 매체가 필요하다고 생각을 했습니다.  
그리고 이왕이면 내 블로그만이 아닌 모든 블로거들이 사용할 수 있도록 만들기로 했습니다.  

# Coupang Ads by Dveamer 

## 1차 구현 

직장인이다보니 주중에 집중적으로 프로그래밍을 할 수는 없었고 일단 광고링크가 어떻게 돌아가는 것인지 관찰을 해봤습니다.  
결제와 환불을 몇차례 진행하며 수익으로 이어지는 필수 파라미터들을 파악했습니다. 덕분에 생수 2L 6통, 만두, 과자 등이 텅텅 비워져있던 냉장고를 하나 둘 채워가고 있습니다.  

실제 1차 구현은 2일정도가 소요됐습니다. 
토요일(1/26)에는 테스트용 HTML과 서버를 만들고 크롤링을 통해 수집작업을 진행했고 일요일(1/27)은 서버를 AWS에 올리고 스크립트를 블로그에 적용하고 CDN, CORS등의 작업 등을 했습니다.  

구현한 내용은 크게 두가지로 나눠집니다.  

### 광고 노출

첫번째는 프로세스는 블로그 화면에 광고를 노출하는 역할입니다. 블로그에 심어진 스크립트가 화면 사이즈 등을 확인하고 노출할 광고 개수를 계산 후 API를 호출합니다. 서버에서는 요청받은 개수만큼 상품을 랜덤하게 조회해서 제공합니다. 그리고 방문자가 광고를 크릭하면 스크립트가 API 호출을 하고 API는 쿠팡 사이트 URI를 추출 후 리다이렉트 시켜줍니다.  

Back-end는 이번에도 Python, Flask을 이용했고 Zappa를 이용해서 AWS Labda에 올렸습니다. 예전에는 업무로직을 작성하는 것보다 환경만들고 HTTP 서버를 띄우는데 시간이 많이 소요됐는데 이제는 AWS Lambda, Zappa를 이용하면 30분이면 간단한 업무로직 구현까지 완성이되서 너무 좋습니다. 지난번에 작성해둔 [Zappa를 이용해 AWS Lambda에 Flask 올리기](https://dveamer.github.io/backend/FlaskZappaAWSLambda.html) 글을 참고해서 쉽게 진행했습니다.  

CDN(CloudFront)을 통한 캐싱 작업과 Route53을 통한 DNS 작업은 토이프로젝트에서 매번 해오던 일이라서 이젠 익숙하게 금방 끝내는 것 같습니다. 이런 작업도 자동화하는 법을 찾아봐야할텐데 이미 손에 너무 잘 익혀져서 시작이 쉽지가 않네요.  

Front-end는 순수 Javascript, HTML, CSS로 진행했습니다. 직장에서 Front-end를 전문으로 하질 않다보니 익숙한 JQuery로 해결해버리고 싶었으나 내 블로그만이 아닌 다른 사람들의 블로그에 올릴 소스이다보니 최대한 가볍게 처리하고 싶어서 순수 Javascript로 진행했습니다.  

노출되는 광고의 형태는 아래와 같습니다. 광고물의 개수는 사이트의 크기에 따라 자동으로 변경됩니다.  

![Coupang Ads](https://lh3.googleusercontent.com/Y4OFN9mj8VlLUq7XOfs4d2i3vSOhJKV86cbSmotZT9mndWZSEyrvEV62P6OsDLToZyxw7QuiorYFNPomqom7Z-WR4t76lYPBTIcRnB8GejjyTQD54jc937Q6uFGFqgG5xolzUaBGIxgUQbhJP5hi0dnSHWKBpu8LIB64LTBvhk7X-KAwm-_KdAVmsDK0f69NWPrxjkdhOgwLzSKIpVr9g8FQsCxHVsgLTS9NydgOQhDYzz6Cjmb-v4PqgMMixvpyj59ioGeYgj2RgAKNJ_E1Y5IR4vHzgCOORkKrKlbt7MqeX1o02jWfQYu7D-_c2afDMQsmp1ZVwaGfmlfcLyg_pj9SEZpshLg8cuJZyk4uk-ITD5axJBy8qGNaA9xBRL0g1-aZkDMfkRuGPZEytgPp6Xx_ZTL4eNuQJlvKtt3UaKHGm-FAimzB2NjqdGR7FrJCiYzaZi6ZZcBI0If3fAAa7YuJVDcGeJad48qjM16DtbgzFzoX3TT9R18foS-BRWvQg4y2HWBRdMe2H9B2NHU4STMaecqcQRYypDXmBoa4SK6rINo5Qz3aWCuj6HdyHhkYb9dEBSYIiFST8UYZs37HWSBSeX8BunY_Nc78KkqKCuGO8Ke92uFTjL2jSTzruPvk0soGhHw7MH3AsHvWrqv0v4GJKHvra67oRv83EoSkS142QR3s571XqjDRLV0IK2KO0R9oEvWXaQn4BKPK_snb21lL=w1066-h385-no)

### 수집 작업

두번째 프로세스는 첫번재 프로세스가 좋은 상품들을 노출 시킬 수 있도록 할인율과 별점 그리고 리뷰 개수등을 기준으로 정제된 좋은 상품들을 수집하는 역할입니다. 하루에 1회 정도 수집을 하고 있습니다. 

수집작업은 Python의 requests와 soup을 이용했습니다. 1차 구현 당시에는 수작업이 꽤나 있었습니다. 완벽하게 자동화하기에는 Python만으로 처리하기에 시간이 너무 오래 걸릴 작업이 있었습니다. HTML Dom을 직접 컨트롤 할 수 있는 Node.js와 Puppeteer를 사용할까 아니면 Python과 Selenium을 이용해서 처리할까 고민하다가 뭘로해도 주말내에 못 끝낼 것 같아서 일부분을 수작업으로 남겨놓고 진행했습니다.  

## 2차 구현 : 백그라운드 작업 개선하기

퇴근 후 밤 시간을 통해 진행했습니다. 일을 열심히만 할 줄 알고 잘은 못하는지.. 칼퇴는 못하고 항상 7~8시쯤에 퇴근해서 집에 도착하면 8~9시 입니다. 씻고 저녁먹고 난다음에 바로 뻗어 잠든 날도 있고 조금씩 작업을 한 날도 있었습니다.  


### 광고 노출 

카테고리 기능을 추가했습니다. 상품을 진열할때 수집된 모든 상품들 중에서 랜덤하게 추출했다면 이제는 블로거들이 선택한 카테고리들 중에서 랜덤하게 추출합니다.  

그리고 Coupang Ads by Dveamer 홈페이지 링크를 광고 하단에 추가했습니다. 제가 입과 키보드로 떠들면서 홍보하기에는 역부족일 것 같아서 진행해봤습니다. 근데 Coupang 상품이라는 느낌이 들어서 모양새도 더 괜찮아진 듯한 느낌입니다.  

![Coupang Ads with homepage link](https://lh3.googleusercontent.com/Jn7_YoCvwX_jZlP2oGxV51H9fiHy5MVEHet8POXPUK0FgyAsd0aPsdTwOkOHjR4YPWJJ2Y2YGuWDMKWIiiAUrlid9gv5ZURGp3AxTCy603sipJDAn5auESF2yCT-gAtjvot4t--UjLjEmvNNiAZLy5kVEY7pR4Csgw7-uZRFNZGzZt5Nlvgvsn53u823Y4g62-D0M7jlp02vrYc0V3hoCtHAAPTGs65f1nl7ash_arcP8RcVES0ffajat7TCDo1O0R5-JYGwMsK7T7_2rfMfPCKI73wx9LwO1O4Etn0nRQc7uMkfUgHTHuIvhxiMFi0A0G89RWY9MJURUzpU2lUNPGcVAV3jjvpJEDnCKU-sZckj3zTF3yTUAalp3ARxgKeV9Ccc2DkrGWvUnZQ63C5s5-It0oFk1PLRQlRM-yJLRN7IzftvAsHHF3YbCqTTpA_au8M17nniTbu8UIVH_0faJSj0YhhJN6S5bFZZn9nOyfdW0eyiye7zLevArB4l137bytlFi7waFvmbbeE2b19H_MF0VAXcAgWmJCA9msXsLvqlsaFvrRgns-JG9Y97NXb4opjqbbJqQHkrbm3vXRCJX67jRHyB5wA22rhAu1lSL3WPlweJmbb6PTLOOVQsCCxyuRLpgcVKnEcspBdMg641794T28AzrElv_FHslNwnqnGeFM-Y02fbc-jg1SPE-F07Bj09NjiDrGm47a34zmSIbewB=w1066-h429-no)

모니터링 작업도 진행했습니다. 일단 광고 노출 수와 클릭 수를 시간별로 기록만 했습니다. 블로거 분들이 사용을 시작했는지를 체크해봐야했거든요. 혹시 큰 변경작업이 있다면 반영 시간등을 고려를 해야하니까요. 나중에 노출 수와 클릭 수가 많아지면 수집작업 과정에서 의미있게 사용 될 수 있을 것 같습니다.  

여기서 수정한 기능들은 Python이나 Javascirpt에서 처리하는 업무로직만 수정하면 되는 내용이었기 때문에 새로운 기술을 도입한 것은 없습니다. 게다가 Flask를 사용했기 때문에 서버를 띄워 로컬 테스트를 쉽게 해볼 수 있었습니다. 이 모든게 Zappa가 AWS Lambda에서 Flask를 사용할 수 있도록 도와준 적분에 가능했습니다. 다시 한번 Zappa를 찬양합니다 ㅋ  

### 수집 작업
  
수집해둔 상품 정보들의 유효기간을 설정했습니다. 수집 된 데이터에 기본 유효기간을 기록하고 상품 클릭과 같은 이벤트가 발생시에 유효기간을 늘려주는 방식으로 택했습니다. AWS DynamoDB에서는 데이터 별로 유효히간을 설정해두면 자동으로 삭제가 되기 때문에 만료된 데이터를 제외하고 노출하는 내용 등의 수정은 필요 없었습니다.  

그리고 수집작업을 개선했습니다. 1차 구현 과정에서 수작업으로 빼둔 내용들 중에서 대부분을 자동화 했습니다. Puppeteer를 사용하면 기존 수집작업을 모두 Node.js로 옮겨야하는지 고민이 있어서 수작업으로 진행하면서 구현을 늦추고 있었는데 그건 그때가서 생각하자라는 식으로 진행해버렸습니다. 그리고 필요한 부분만 Puppeteer를 이용하고 스케쥴링으로 잘 처리하면 문제가 없을 것 같네요.  

## 3차 구현 : 네이버 전용 Coupang Ads 만들기

설연휴를 앞두고 금(2/1), 토(2/2), 일(2/3) 동안 진행하고 월(2/4), 화(2/5)에는 장인장모님댁과 부모님댁에 갔다가 집으로 돌아와서 조금씩 다듬었습니다.  

몰랐었는데 네이버 블로그는 구글 애드센스를 사용할 수가 없었다고 합니다. 네이버에서 제공하는 애드포스트를 이용해서만 광고수익을 얻을 수 있었다고 하네요. 기껏 만들어놨는데 우리나라 블로그 조회수의 대부분을 차지하는 네이버 블로그에 적용할 수 없다고 하니 굉장히 안타까웠습니다.  

그래서 좀 더 자세히 들여다 봤더니 위젯이라는 것을 이용해서 anchor(```<a>```) 태그와 img 태그는 쓸 수 있는 것 같았습니다. 서버에서 몇가지 트릭만 부려주면 구현이 가능할 것 처럼 보여서 진행해봤습니다.  



### 광고 노출 

브라우저에서 화면이 랜더링되면서 img태그의 src로부터 이미지리르 로딩해오는데 보통 src의 목적지는 정적인 이미지 파일의 위치입니다. 근데 src 목적지를 서버로 해두고 서버에서 상황별로 다른 이미지 파일 위치로 리다이렉트 시키도록 만들었습니다. 그리고 anchor 태그도 동일하게 상황별로 다른 쿠팡 상품 사이트로 리다이렉트 되도록 만든거죠.  

가장 먼저 해결 할 사항은 이미지와 쿠팡 상품 사이트의 정보를 일치시키는 것입니다. 예를들어 키보드 이미지를 클릭했는데 물병 판매하는 사이트로 연결되면 안되겠지요.  
네이버 블로그는 script를 사용할 수 없는 상황이기 때문에 img 태그와 anchor 태그 그리고 서버영역의 코딩만으로 해결해야했습니다. 마치 네이버가 내는 퀴즈를 푸는 느낌이었달까요? 제가 내놓은 답은 서버영역에서 cookie에 몇가지 정보를 보관해달라고 브라우저에게 요청 하는 것이었습니다. Cookie는 예전부터 웹에서 많이 사용되는 기능인데 img, anchor 태그를 위해 cookie를 사용한 적은 처음이었습니다. 이와 관련해서 CDN을 통하면서 몇가지 문제가 발생했는데 세세한 문제이므로 생략하겠습니다.  

### 이미지 생성 작업

상품의 이미지는 쿠팡에서 이미 제공하고 있기 때문에 연결만 시켜주면 되는데 상품 이름, 가격, 할인률 등이 문제였습니다.  

Script를 사용할 수 있다면 상품 이름을 text로 내려서 가볍게 처리하면 되지만 퀴즈의 전제조건은 img 태그와 anchor 태그만으로 상풍명을 출력하라였습니다. 제가 내놓은 해법은 상풍 이름, 가격, 할인률을 포함한 이미지를 제가 미리 만들어 두는 것이었습니다.  

앞서 개발해둔 수집작업이 끝나고 나면 실행해야할 이미지 생성 작업이 하나 더 생긴 셈입니다. 개인적으로 재미있던 점은 상품 1개를 위한 이미지 생성에 거의 딱 1초 정도가 걸린다는 점입니다.  

이미지 생성에는 Pillow라는 Python 라이브러리를 이용했습니다. 상풍 이름 이미지를 만드는데 상품 이름이 길어지면 두줄, 세줄이 되고 가운데 정렬도 해줘야하고 등등의 문제가 많았지만 Pillow는 이미 모든 기능을 제공하고 있었습니다. 그렇게 만든 상풍 이름 이미지와 가격 이미지, 할인률 이미지를 상품 이미지와 합치고 AWS S3에 저장했습니다.  

아래는 [제 네이버 샘플 블로그](https://blog.naver.com/dveamer) 화면 입니다. 좌측에 진열된 상품 3개가 Coupand Ads를 이용한 광고입니다.  

![네이버 샘플화면](https://sda.dveamer.com/images/guide_n_09.png)  

#### References

[pillow - Image API](https://pillow.readthedocs.io/en/stable/reference/Image.html)

[Create images with Python PIL and Pillow and write text on them](https://code-maven.com/create-images-with-python-pil-pillow)

[Center-/middle-align text with PIL?](https://stackoverflow.com/questions/1970807/center-middle-align-text-with-pil)

[Adding borders to an image using python](https://stackoverflow.com/questions/11142851/adding-borders-to-an-image-using-python)

## 홍보

현재는 네이버 블로거 분들을 대상으로 홍보를 진행 중입니다. 뭐든지 깊게 들어가면 어렵지만 이 영역은 시작부터 어려운 것 같습니다. 트위터, 페이스 북에 홍보 글은 남겨봤는데 역시나 큰 호응이 없었네요. 지금은 결국 네이버에서 "네이버 쿠팡 파트너스"로 검색되는 블로그에 들어가서 댓글로 홍보 글을 남겨놨습니다. 무식하게 15페이지 넘게 조회 됐는데 하나하나 다 들어가서 남기고 온 것 같네요. 처음에는 민폐일 것 같아서 망설였는 쿠팡 파트너스 사용 방법에 대해서 글을 작성하신 분들이니까 Coupang Ads가 도움이 될 수도 있을 것 같아서 남기게 됐습니다. 홍보가 효과가 있을지는 모르겠네요. 블로거 분들에게 유용한 점이 있다면 효과가 있겠죠..!?  

그리고 저처럼 Github Pages를 이용하시거나 직접 블로그를 만들어 운영하시는 분들에게 홍보를 하고 싶은데 어떻게 해야할지 모르겠군요.  
티스토리, 미디엄 등 다른 블로그에서 사용이 가능한지도 체크해봐야할 것 같고요. 일단 가입부터 해봐야겠군요 ㅎ  

# 소감

토이 프로젝트라는 것이 실력향상, 경험 그리고 용돈벌이 등을 목적으로도 하지만 저는 만족감을 위해서 이기도 합니다.  
Perfect Trainer 앱을 만들었을 때도 제가 포켓몬고를 더 재미있게 하기 위해서 만들기를 시작했지만 주변 사람들이 사용하고 즐거워 한다는 것에 굉장히 큰 만족감을 느낄 수 있었습니다. 그래서 이번에도 제 블로그에 광고를 달기 위해서 시작했지만 조금 더 다듬어서 다른 블로거들도 사용할 수 있도록 만들어봤습니다.  

많은 블로거 분들이 이용해주시고 많은 피드백을 받을 수 있으면 좋겠습니다.  

# 딴 소리

그리고 쿠팡 혹은 비슷한 온라인 쇼핑몰 관계자분이 이 글을 보신다면 모바일 광고 관련해서 꽤나 기발한 아이디어가 하나 있습니다. 몇년 전부터 갖고 있던 아이디어인데 개인으로서는 써먹을 방법이 없네요. 그렇다고 공짜로 공개할 수는 없고.. 그렇습니다.. 뻘쭘하네요 ㅋ  


