---
layout: post
title:  "5차 퀀트투자 수익, 수익률 공개 : 하위20% 소형주 + 고성장 + 분기 PFCR 저벨류 + 고마진"
date: 2022-08-21 00:00:00
lastmod: 2022-08-21 00:00:00
categories: Investing
tags: Investing Quant-Investing
hidden: true
published: true
---

하위20% 소형주 + 고성장 + 분기 PFCR 저벨류 + 고마진 이라는 전략으로 퀀트투자를 진행한 결과를 기록하는 페이지입니다.  
평가 수익, 수익률을 공개 합니다.  

<!--more-->  

google spreadsheet 의 googlefinance 기능을 이용했기 때문에 실시간 현재가를 출력하지 않습니다.  
주식시장이 종료되고 난 후 넉넉히 시간을 두고 오후 4시 40분 이후에 확인하는 것이 좋습니다.  

## 투자 전략

  * 전략 : 하위20% 소형주 + 고성장 + 분기 PFCR 저벨류 + 고마진
  * 리밸런싱 주기 : 분기별(3/31, 5/31, 8/31, 11/30)
  * 종목 개수 : 20 종목
  * 제외 업종 : 해외본사(중국기업 등), 스팩, 금융, 지주사 업종
  * 거래정지시 손실처리율 : 100%
  * 수수료 + 슬리피지 : 1%

## 백테스트 결과 

  * 백테스트 툴 : [퀀트킹](http://www.quantking.co.kr)
  * 백테스트 기간 : 14개년 (2008~2022)

|연평균 수익률|월평균 수익률|누적 수익률|MDD|거래정지 종목수|
|---:|---:|---:|---:|---:|
|47.7%|3.6%|23,488.2%|33.1|1|


## 변경 사항 

### 매수 시기

8월 21일 퀀트킹 기준으로 이미 96.6%의 2분기 공시정보가 업데이트가 완료된 상황이라 8월 22일 리밸런싱을 진행했습니다.

### 매수 제외 기업

10년치 공시자료를 기준으로 확인결과,  

유상증자(1곳), 전환사채(1곳),  잦은 자기주식처분(1곳), 공시자료 정정(1곳) 에 대해서 제외를 결정했습니다.  

제외된 전환사채 1곳은 사채만기일이 2022.10 이여서 제외했지만  
전환사채 건을 가진 회사가 3곳 더 있었는데 만기일이 4분기 투자와 무관했기 때문에 제외하지 않았습니다. 19%, 17%의 주식수에 해당되는 권리행사를 할 수 있는 규모라서 고민을 했지만 만기 시기가 워낙 많이 남았기 때문에 제외하지 않기로 했습니다.  

잦은 자가주식처분한 회사는 2021년도에 7찰례 처분했고 공시 시점과 처분 시점이 일주일 정도밖에 차이가 나지 않아서 4분기 발생 가능성을 확인할 수 없어서 제외했습니다.  

공시자료 정정한 회사는 첫 기재시 기재 금액의 단위와 부호가 잘못 기재되어 정정됐는데 그 중 이익이 +6억에서 -6억으로 정정되었음에도 퀀트킹에는 첫 기재 금액인 +6억으로 반영되어있어서 제외했습니다.  

그 외에도 무상증자(1곳), 자기주식처분(2.21%) 등의 특이사항이 있었지만 4분기 투자에 영향 없을 것으로 예상됩니다.  

### 5일간 평균거래대금

지난 번에는 5일간 평균거래대금이 0.1억 이상인 건들을 대상으로 했습니다.  

## 종목별 수익률 현황

아래 수익률 현황은 매수, 매도 수수료 같은 비용 계산을 포함하지 않은 정보입니다. 또한 실제와 약간의 오차가 있을 수 있습니다.  

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1967941242&amp;single=true&amp;widget=true&amp;headers=false" style="width:100%;min-height:700px;max-height:2200px;"></iframe>
<!--ads-->  

## 수익률 그래프 

투자금 추가가 없는 관계로 그래프는 지난 투자와 이어서 작성합니다.  

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1631942239&single=true" style="width:100%;min-height:500px;max-height:2200px;"></iframe>  


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1057887183&single=true" style="width:100%;min-height:500px;max-height:5000px;"></iframe>  


