---
layout: post
title:  "7차 퀀트투자 수익, 수익률 공개 : 하위20% 소형주 + 고성장 + 분기 PFCR 저벨류 + 고마진"
date: 2022-11-20 00:00:00
lastmod: 2022-11-20 00:00:00
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
  * 리밸런싱 주기 : 분기별(3/31, 5/15, 8/15, 11/15)
  * 종목 개수 : 20 종목
  * 제외 업종 : 해외본사(중국기업 등), 스팩, 금융, 리츠, 지주사 업종
  * 거래정지시 손실처리율 : 100%
  * 수수료 + 슬리피지 : 1%
  * 5일간 평균거래대금 : 0.1억 이상

## 백테스트 결과 

  * 백테스트 툴 : [퀀트킹](http://www.quantking.co.kr)
  * 백테스트 기간 : 20개년 (2003~2023)

|연평균 수익률|월평균 수익률|누적 수익률|MDD|거래정지 종목수|
|---:|---:|---:|---:|---:|
|40.6%|3.2%|90,962.8%|44.1|3|

가능한 백테스트가 14개년에서 20개년으로 늘어나서 투자기간이 늘어남에 따라 누적 수익률이 크게 늘어난 것 처럼 보이게 됐고 2008년 경제불황이 포함되면서 MDD가 크게 늘어났습니다.  

## 변경 사항 

### 매수 시기

5, 8, 11월에는 15일이 공시마감일이지만 3월은 31일이 공시마감 일이라는 것을 이번에 처음 알았습니다.  
당연히 그 이후 90%이상의 공시정보가 퀀트킹에 업데이트되어 4월 3일 리밸런싱을 진행했습니다.  

### 퀀트데이터 vs 백테스트

퀀트킹 업데이트 과정에서 퀀트데이터 로직, 백테스트 로직이 제거되어 다시 세팅하는 과정을 거쳤습니다.  
그 이후 둘 데이터가 동일하게 나오는 것을 확인했습니다.  


### 매수 제외 기업

10년치 공시자료를 기준으로 확인결과,  

유상증자(3곳), 신주인수권부사채(1곳) 관련하여 염려되는 기업들을 제외했습니다.  


## 종목별 수익률 현황

아래 수익률 현황은 매수, 매도 수수료 같은 비용 계산을 포함하지 않은 정보입니다. 또한 실제와 약간의 오차가 있을 수 있습니다.  

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1967941242&amp;single=true&amp;widget=true&amp;headers=false" style="width:100%;min-height:700px;max-height:2200px;"></iframe>
<!--ads-->  

## 수익률 그래프 

투자금 추가가 없는 관계로 그래프는 지난 투자와 이어서 작성합니다.  

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1631942239&single=true" style="width:100%;min-height:500px;max-height:2200px;"></iframe>  


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1057887183&single=true" style="width:100%;min-height:500px;max-height:5000px;"></iframe>  



