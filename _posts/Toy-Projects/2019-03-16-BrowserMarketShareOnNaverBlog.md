---
layout: post
title: "네이버 블로그 방문자들이 사용하는 브라우저"
date: 2019-03-16 00:00:00
categories: ToyProject
tags: ToyProject 개발기
---

![네이버블로그 브라우저 점유률](https://lh3.googleusercontent.com/wmKOE1OhSZsXJQcAHOgBAITQ9GTxQPkK8PIHtRopjFSQOoxqVdj0kP6-JTKNAPS0DFmFq3tNObTrulBfjuu__IlY-wU-snIv4KuJpflzVRSuAAL1WRf6TqEPLCqwL93br2uMZ3yFlHLRZaQ0fhSq-2CYgNYcw7QkZLy8sSH7jkgte8X5N-hqwAB6oY_m1ogW4dufoVM5Q_n8_FIvnACNvWN60nBo8FqAdOgFC4z5dnkEUFvE_OuYNaj9-4KWuPua-Fb20W9I5-WfyLgEgi8NpQ16OMj9AfN4Pl_HAbEtJ9Pfgtjd1CsVvJ7xsra9wHQSow2GFoasqlPgayCgG14S8xuoXTs43S2tCcdaCiBT-mUbmCqga-BTAANN7npuYvpd6AP8TsnPCWjo08n5nrYKIWaTAWL1oaAVzPbngyOhnKKjTdFxrqXr82I2Tn5C-C_bJ2Hpp4tTbVsLGk80x2X9gom6vH2z4Im6OQ7w_zik_iRQveuRvtdeSQ_HGmrxVlyFYj9q_4QB-Nn9ur2QYz3H2jVvRSfqmnN0cvb78v62PlOKgbOSOQjLEW8fACsu5e-YcxfCHnEDB2cFDhVrN_yvuQ5UZRPTiGr82sg9cavZh_xeb-xrBhvCU3PxM0SM-_5JKUFN7eZbQXbKfLlUKgCOdTa0uN7PC5xXwAjtVab4DwfLT1mqcRaTEp-HOKE76f4iZVyfj-hiDjF5ZjP7givP_z0O=w1026-h806-no){:class="imgTitle"}  


네이버 블로그 방문자들이 주로 사용하는 브라우저는 어떤 것일까요?  

<!--more-->

최근에 쿠팡 상품을 네이버 블로그에 자동광고 가능하도록 하는 서비스를 만들었습니다. 그리고 몇몇 블로거 분들이 자신의 네이버 블로그에 광고를 달아주셨습니다.  

그러다보니 네이버 블로그 방문자들이 사용하는 브라우저가 점유률을 알게 되었습니다. 물론 점유률을 논하기에는 너무나 방문자 수도 적고 유입되는 블로그의 개수도 너무 적긴합니다만 결과가 너무 예상 외라서 글을 남겨보기로 했습니다.  


![네이버블로그 브라우저 점유률](https://lh3.googleusercontent.com/wmKOE1OhSZsXJQcAHOgBAITQ9GTxQPkK8PIHtRopjFSQOoxqVdj0kP6-JTKNAPS0DFmFq3tNObTrulBfjuu__IlY-wU-snIv4KuJpflzVRSuAAL1WRf6TqEPLCqwL93br2uMZ3yFlHLRZaQ0fhSq-2CYgNYcw7QkZLy8sSH7jkgte8X5N-hqwAB6oY_m1ogW4dufoVM5Q_n8_FIvnACNvWN60nBo8FqAdOgFC4z5dnkEUFvE_OuYNaj9-4KWuPua-Fb20W9I5-WfyLgEgi8NpQ16OMj9AfN4Pl_HAbEtJ9Pfgtjd1CsVvJ7xsra9wHQSow2GFoasqlPgayCgG14S8xuoXTs43S2tCcdaCiBT-mUbmCqga-BTAANN7npuYvpd6AP8TsnPCWjo08n5nrYKIWaTAWL1oaAVzPbngyOhnKKjTdFxrqXr82I2Tn5C-C_bJ2Hpp4tTbVsLGk80x2X9gom6vH2z4Im6OQ7w_zik_iRQveuRvtdeSQ_HGmrxVlyFYj9q_4QB-Nn9ur2QYz3H2jVvRSfqmnN0cvb78v62PlOKgbOSOQjLEW8fACsu5e-YcxfCHnEDB2cFDhVrN_yvuQ5UZRPTiGr82sg9cavZh_xeb-xrBhvCU3PxM0SM-_5JKUFN7eZbQXbKfLlUKgCOdTa0uN7PC5xXwAjtVab4DwfLT1mqcRaTEp-HOKE76f4iZVyfj-hiDjF5ZjP7givP_z0O=w1026-h806-no)  

위 그래프는 [Coupang Ads](https://sda.dveamer.com/guide_naver_blog.html) 출력을 위해 AWS Cloud Front으로 접속한 방문자들이 사용한 브라우저의 통계정보 입니다. Internet Explorer가 50% 이상으로 가장 높고 Chrome이 약 40%를 차지하고 있습니다.  

이 자료를 해석하기 전에 참고해야할 점이 몇가지 있습니다.  
일단 하루 유입수가 1만건이 안될정도로 매우 적습니다.  
그리고 제가 제공하는 광고서비스는 네이버 블로그용이 있고 일반 블로그용이 있고 위 그래프는 두 서비스의 유입을 모두 합친 결과지만 네이버 블로그용의 유입이 일반 블로그용보다는 월등히 높습니다.  
네이버 블로그용의 특이사항은 데스크탑에서만 접근이 가능하고 블로그별로 출력하는 광고 개수마다 방문자 1명당 서비스 호출 횟수가 다릅니다.  

결론적으로 이 자료를 해석해서 얻을 수 있는 의미있는 결론은 "데스크탑을 사용하는 네이버 블로그 방문자들 중에는 예상 외로 Internet Explorer 사용자가 많다." 정도인 것 같습니다.  


비교를 위해 "브라우저 사용률"로 구글링해보면 [https://www.koreahtml5.kr](https://www.koreahtml5.kr/front/stats/browser/browserUseStats.do), [http://gs.statcounter.com](http://gs.statcounter.com/browser-market-share/desktop/south-korea) 같은 사이트에서 한국지역 데스크탑 브라우저 사용률은 크롬이 70%정도고 IE가 20%정도라고 알려줍니다. (2019년 2월 기준)  

비교할 수 있는 자료를 하나 더 보여드리겠습니다.  

![블로그 브라우저 점유률](https://lh3.googleusercontent.com/FBgMCoGDWg9EaLFmsF6uo5CauwJbKAgfkQSkdWF43ODC8wguJ48gCcY4_r8K5SMO89mHFjdPP9g13eHDzx6K0CA5IG7oXwr0xjBc0B2zf4lqYDOhdQkGbEH6JDT2BDYuNKVVEEQkvhc0MHzoDizIYpHmrMVnpjX9UGaAwscl85LZY8XB-uYJ7bcpanEsPyWWm-Gve7gakZG4xj2ueWI7bQZkDm3t1kwy06t3PiVNC6smrtGkOzXtPNg4slBEXL4JH79nZHsa9H61X5HriIM74GxJQPLFAmuQ15dyXJe748VjxliDYi2Gzki-_6R8kSDzmK4Ni7X5l9T7AS6zqUqogb8PzVvQ3x2Dvphresse6n12eHWQB_KvdejcMU4ahhLrOUeQRnLVkQMVtEP3-Z9YasyO3UF5O58_u61mVMn8jZ-7eu3tGEB27aYSsZXyVBOighoOBILetZYrrx0C9HXqxNDgx0F9QNo5R5M9UShGbyxNnHnx7q-Ga8it3VcsqUpW40TJfv_KXYsVo3PtWe7zEKfu1LkqQGAtKlWSweoXZ-wY61Ebe9qrIPnkCvRCRd-d6-XrtmUDVMlvI0o-HQOWi3HiwDLvIyqB0TBq4kfiFIdKxoTQiI5xiwquf8MCd0xO-ROemMbi0oOHezQDO1OyZgUSMVtvW-j_ehug5oY-3pLTFsJqzpDJ14RlUUEPzh7iPbmDAlI4h7797onsMoRsvldA=w762-h982-no)  

위 그래프는 Google Analystics에 수집된 현재 보고 계신 제 블로그(dveamer.github.io)의 방문자 브라우저 점유률 입니다.  
전체 접속자는 438명이고 IE는 23명으로 대략 5% 정도 밖에 되지 않습니다. 크롬은 대략 80% 정도네요.  

사실 저는 제 블로그의 통계정보를 계속 봐왔기 때문에 국내 IE 사용률에 대해서 잘못된 정보를 가지고 있었습니다. 사람들이 IE를 거의 사용하지 않는줄 알고 있었고 네이버 블로그에서 제 광고 서비스로 유입되는 호출량이 전체의 50%나 되는 줄 전혀 모르고 있었습니다.  

그러다보니 광고 서비스를 만들고 테스트 하는 과정에서도 IE에 대한 테스트는 진행하지도 않았습니다. 근데 2월 말쯤에 광고 서비스 통계정보를 확인하고 부랴부랴 테스트하고 동작하지 않는 일부 기능을 수정했습니다. 이미 블로거 분들에게 열심히 홍보를 하고 1~2주 뒤에 버그를 수정했기 때문에 굉장히 마음이 아펐습니다. 혹시 저와 같은 상황을 겪으실 분들이 계실까봐 글을 남겨놓습니다.  

네이버 블로그 플랫폼을 운영하시는 네이버 직원분들은 훨씬 더 정확한 정보를 가지고 계실 것이고  
네이버 블로그에 글을 올리는 블로거분들은 Javascript를 쓰실 일이 없기 때문에 어떤 브라우저에서 접속이 이뤄지는지 알 필요가 없으실겁니다.  

네이버 블로그로부터 유입이 이뤄지는 사이트를 운영하시는 분들이라면 데스크탑 IE 사용자가 생각보다 더 많을 수 있다는 점을 잊지 마세요.  


### 참고사항 1

제 광고 서비스 중 네이버 블로그용 유입이 일반 블로그보다 월등히 높다고 위에서 설명했는데 오해의 소지가 있을 수 있어서 추가 설명을 드립니다.  
국내 블로그들 사이에서 네이버 블로그의 점유율이 월등히 높다는 얘기는 아닙니다. 국내 블로그 점유율을 저는 정확히 아는 바가 없습니다.  

최근 쿠팡에서 정식으로 다이나믹 광고라는 기능을 내놓으면서 제 일반 블로그용 광고 서비스가 사실상 무의미해졌기 때문에 일반 블로거분들은 많이 옮겨가셨습니다. 그로 인해서 거의 네이버 블로그에서만 서비스가 되고 있는 상황입니다. 다행히(?).. 네이버 블로그용은 네이버 블로그가 제약사항들을 풀기 전까진 제가 제공하는 서비스가 블로거 분들에게 도움이 될 것이라고 생각합니다.  


### 참고사항 2

위에서 비교대상의 예로 들은 제 블로그의 특이사항을 몇가지 설명드립니다.  

제 블로그는 프로그래밍 관련 기술주제를 다루다보니 방문자들이 특색이 명확합니다.  
모바일에서의 유입이 굉장히 적고 주중 업무시간(9시~18시)의 방문자수가 다른 시간대보다 월등히 높습니다.  
대부분 프로그래밍하는 직장인들이 일하다가 구글링을하고 접속하는 것으로 추측됩니다.  

방문자들의 특성상 IE 사용자가 더 적어서 5%라는 굉장히 낮은 수치가 나온 것 같습니다.  

네이버 블로그도 어떤 컨텐츠를 다루는 블로그냐에 따라 방문자들이 사용하는 브라우저 점유률은 다를 것입니다.  
위에서 50%라고 보여지는 제 광고 서비스의 IE 점유률은 굉장히 특수한 상황에서 나온 결과일 수도 있습니다.  
제 광고 서비스를 설치해주신 블로그의 수가 아직 그렇게 많지 않아서 다양한 컨텐츠를 가진 블로그에 대한 통계정보가 아닙니다.  
또한 블로그들 사이에서도 방문자수가 균일하지 않고 방문자 수가 월등히 높은 몇몇 블로그의 방문자 특성이 반영되었을 가능성이 높습니다.  

정확한 수치는 네이버 직원분들이 알고 계실 것이고 제가 보여드린 수치는 "그럴수도 있겠구나" 라는 정도로 참고하시기 바랍니다.  

지인찬스를 써서 물어보고 싶기도 하지만 그러면 재미없으니 제 광고 서비스가 좀 더 많은 블로그에 설치되면 다시 한번 글을 써보도록 하죠.  



