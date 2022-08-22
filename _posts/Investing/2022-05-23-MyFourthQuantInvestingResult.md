---
layout: post
title:  "4차 퀀트투자 수익, 수익률 공개 : 하위20% 소형주 + 고성장 + 분기 PFCR 저벨류 + 고마진"
date: 2022-05-23 00:00:00
lastmod: 2022-05-23 00:00:00
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
|49.2%|3.7%|27,110.6%|35.5|1|


## 변경 사항 

### 매수 시기

많은 기업들이 5월 16일 이미 1분기 공시를 완료한 것을 확인했고 5월 22일 퀀트킹 기준으로도 96.7%의 실적 업데이트가 완료된 상황이라 이른 시기에 리밸런싱을 시작했습니다.  

### 매수 제외 기업

기존에는 1년치 공시자료를 기준으로 확인을 했지만 전환사채를 좀 더 제대로 식별하고자 5년치의 공시자료를 기준으로 확인했습니다.  

그 결과 유상증자(1곳), 전환사채(6곳), 자기주식처분(1곳) 이 발견되어 매수대상에서 제외했습니다.  

5년치 공시자료로 조사를 하다보니  
전환사채가 존재함에도 불구하고 2차 리밸런싱 때 투자했고 14%의 수익을 냈던 회사 1곳이 발견됐습니다.  
그 때는 1년치 공시자료로만 조사하다보니 유효한 전환사채가 있는 것을 발견못했고 투자했으나 수익은 본 셈이었습니다.  
게다가 전환사채의 마지막 전환 가능날짜가 2020.06.11 로 이번 리밸런싱 후 곧 주식으로 전환 기회는 마감되는 것으로 보여졌습니다.  

고민이 많이 되었지만 채권자가 주식으로 전환할 가능성은 존재하고
계산해보니 (제대로 한것인지 모르겠지만) 현재 주식가격으로 전환시 얻는 금액과 만기보장수익률에 의한 금액을 비교해보니 전환시 얻는 금액이 2억정도 높은 것으로 보여 해당 기업은 리스크가 높은 것으로 판단하고 제외 했습니다.  

### 5일간 평균거래대금

지난 번에는 5일간 평균거래대금이 0.5억 이상인 건들만 대상으로 했지만 
이번에는 0.1억 이상인 건들을 대상으로 했습니다. 사실 이 기준은 500만원 투자 기준으로 잡은 것인데 2,500만원 투자로 변경하면서 기준 변경을 못한 상황입니다.  
매수 과정에서 시간이 크게 지체된 건은 없어서 추후 매도 시에도 큰 문제는 없을 것으로 생각하고 있습니다.  

## 종목별 수익률 현황

아래 수익률 현황은 매수, 매도 수수료 같은 비용 계산을 포함하지 않은 정보입니다. 또한 실제와 약간의 오차가 있을 수 있습니다.  


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1967941242&amp;single=true&amp;widget=true&amp;headers=false" style="width:100%;min-height:700px;max-height:2200px;"></iframe>
<!--ads-->  

## 수익률 그래프 

투자금 추가가 없는 관계로 그래프는 지난 투자와 이어서 작성합니다.  

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1631942239&single=true" style="width:100%;min-height:500px;max-height:2200px;"></iframe>  


<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vRHclJcL_QjTWm0g7gGzg-zn501Naf9ooeW5baGNkW86TSpbHulGFBWhZr77I9qk_HN7apM5oJSyUOg/pubhtml?gid=1057887183&single=true" style="width:100%;min-height:500px;max-height:5000px;"></iframe>  


