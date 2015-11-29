---
layout: post
title:  "Lecture Of Node.js"
date:   2015-07-06 12:00:00
categories: Node.js
---
![NodeJs](https://nodejs.org/images/logos/nodejs-dark.png "NodeJs Logo")

* 강사
 - (주)에이티지랩 문성훈 이사 
 - moon9342@atglab.co.kr

# Tools
## WebStorm
 * 유료
 * http://www.jetbrains.com
 * File > setting
  - Editor > File Encodings : UTF-8
  - Langueages & Framework > Node.js and NPM : interpreter & core modules
  - Langueages & Framework > Javascript > Libraries : ECMAScript 6, Node.js Golbal 등 추가 체크

# Node.js Introduction
## Chrome browser 의 javascript engine V8 기반
 * C++로 개발된 오픈소스
 * Garbage Collection 에 의한 메모리 관리 (완벽하진 않음)
 * compile 해서 최적화하는 구조
 * 고성능 standalone 사용가능

### Week Point
 * Javascript spec상 코드 명확성이 떨어짐

## 빠르고 확장 가능한 Network 프로그램 개발에 특화

## Singgle Thread 기반의 Non-Blocking I/O model 사용
 * 비동기 방식의 프로그래밍
 * 연산작업이 적은 업무에 적합 ( 예: SNS )
 * 연산작업은 다른 thread에서 처리하도록 작업필요
 * 많은 request가 있을 때 장점 발휘

### Week Point
 * Client 접점에서 call을 받는 부분은 block방식으로 진행됨
 * Call 받는 부분에서 연산작업이 많으면 다음 call 을 받지 못함

## Event Driven Programming Model
 * Async module을 이용하면 콜백지옥을 어느정도 해결가능
 * 비동기 방식으로 프로그래밍해야함

### Week Point
 * 콜백지옥은 디버깅이 어려움
 * 비동기 방식으로 프로그래밍하지 않으면 동기방식의 기존 프로그램과 성능차이 없음

# Module System
 * Web MVC Framework : express, krakenjs, MEAN.IO
 * template Engine : ejs, Jade, hogan
 * Logging : winstone, morgan
 * Testing : nodeunit, mocha
 * Authentication : passport
 * building script : grunt
 * DBMS : mysql, mongodb + mongoose
 * Clustering : cluster
 * Callback processr 관리 : async

## Global Objects
 * 전역객체
 * object 뿐만 아니라 function, variable 형태도 존재
 * variable 형태의 global objects 는 module scope를 가짐
 * 그 외의 global objects 는 global scope를 가짐
 
### Sample
 * global{ object }
  - config 같은 global하게 사용해야하는 내용 저장가능
 * cosole{ object }
 * __filename{ string }
  - 해당 파일의 파일명
  - local scope
 * __dirname{ string }
  - 해당 파일의 디렉토리 명
  - local scope 
 * process{ object }
 * events{ object }
  - addListener(key,function)
  - on(key,function)
  - emmit(key)
  - removeListener(key)
  - removeAllListener()
 * os{ object }  
 * fs{ object } : file system
 
## Customized Module
 * 모듈은 파일단위로 생성됨
 * 모듈이 또 다른 모듈을 포함되는 형태로 구조화 가능
 
### Load Module
 * require("./circle");
  - ./circle.js 파일 검색
  - ./circle/index.js 파일 검색
 * require("circle");
  - node_modules folder내에서 검색
  
### Structure
 * require 로 특정 모듈을 실행시 아래 처럼 wrapping된 method가 실행됨
   - 동일한 모듈이 두번 require 되면, 한번만 불러오며 singleton형태로 호출됨

```
(function(exports, require, module, __filename, __dirname){
	// 특정 모듈 파일의 컨텐츠
}) (); 
```

 * exports 
  - module.exports 객체에 대한 reference
  - 사용하지 않는 것이 좋음
  - 사용할거면 module.exports 와 혼용하지 않는 것이 바람직함

 * module
  - module.exports : client 모듈로 노출시킬 내용을 담아줌

```
module.exports.methodNm = function(){ };
module.exports.variableNm = 100;
```
    
# ETC
## package.json
 * project 에 대한 설명
  - 띄어쓰기 허용안함
 * project 에서 사용할 module 정보 기술
  - run NPM install/update 을 이용해서 dependecy를 고려해서 다운받음

## npm
 * install
 * update
 * prune 

# Node.js Web Application
## Express Module
### Features
 * 초경량
 * 유연함
 * Web Application Framework
 * REST API개발에 최적화
 * Web application 을 MVC pattern 으로 구성하도록 도와줌

### Functions
 * Routing
 * Template 을 이용한 View 처리
  - ejs 를 이용한 view 구성 : res.render("viewName", { // 전달 객체 } );
 * Reqeust Handler
  - Parameters parsing : req.param( /:paramName/* ), req.query, req.body(body-parser)
  - File system : fs, connect-busboy
  - Session : express-session
  - Cookie : cookie-parser

# Node.js Persisting Data
 Node.js는 NoSQL 종류와 궁합이 잘 맞지만 Node.js 강의을 위해 대부분 잘 알고 있는 RDB 종류로 진행
## MySQL
### Mysql Module

### Generic-pool Module

```
pool.acquire(function(err,conn){
  // execute query
});
```

# Node.js Essential Module
## RSA
 * node-rsa module
 * jsencrypt.js : browser encryptor

## WebSocket
 * WebSocket 이 http(ajax long polling) 보다 성능 좋음
 * socket.io : client, server
 * multi porting 방식 : 하나의 port 에 여러개의 connection을 맺을 수 있음

#### References
 * http://bchotistory.com/896

## Clustering
 * CPU 1 core가 하나의 node process를 수행 가능
 * Single Thread의 특성상 Quad Core 에서 실행시키면 1개의 Core만 일하게 됨
 * Clustering 기능은 아직 unstable 단계
 * 현재까진 roundrobin 으로만 가능
 * Cluster worker 간의 context 공유가 안됨
  - http 통신만해도 roundrobin 으로 분산되기 때문에 handshake 조차 이뤄지지 못함
  - Redis 와 함께 사용

## Async
### Parallel Method
 * 여러 비동기 task가 모두 완료된 후 다음 task 를 수행할 수 있도록 도와줌

### Series Method
 * 2개 이상의 task가 순차적으로 실행됨

### Waterfall Method
 * 2개 이상의 task가 순차적으로 실행됨
 * 첫번째 task의 결과가 두번째 task에 전달됨


## Passport Module
 * 인증 모듈
### Local Strategy
 * ID, Password 를 비교하는 정책

# 용어
 * I/O
  - 주로 File, Socket, DB에 대한 in/out
 * 1급함수 기능
  - function을 파라미터, 리턴값 등으로 사용하는 기능
  - javascript의 대표적인 특징 
 * Module
  - Library 와 비슷한 개념
 * NPM
  - Node Package Manager
  - module간의 dependency 충돌 문제 해결해줌 ( JAVA 의 Maven 과 같은 역할 )

# Question
 * 2 tier가 아닌 3 tier로 설계할 경우 문제점
  - 결국 비동기방식으로 프로그래밍했냐 안했냐가 중요할듯
 * require -> singleton ?
