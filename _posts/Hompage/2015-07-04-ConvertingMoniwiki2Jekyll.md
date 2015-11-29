---
layout: post
title:  "Converting Moniwiki to Jekyll"
date:   2015-07-04 12:00:00
categories: blog jekyll
---

Jekyll 로 블로그를 만들어보려고 하는데 기존에 사용하던 Moniwiki 페이지가 버리기엔 아깝고 둘다 쓰기에는 벅참
Moniwiki를 Jekyll 로 변환해보기로 함

# Plan

## HTML 2 Markdown
 * 한글제목의 페이지 때문에 생긴 아스키코드 형태의 링크들의 리스트 추출. 한글 원제목도 함께 추출
 * 크롤러를 구현해서 기존 Moniwiki의 모든 HTML의 페이지를 저장 ( script, css 제외. only HTML )
 * 파일명과 링크를 가시적인 영문으로 변경할 방법 모색
 * 모든 HTML 문서를 Makrdown 문서로 변환
 * 모든 Markdown 문서에 Jekyll header 추가
 * Moniwiki 페이지들의 링크구조를 Blog 형태에 맞게 재구성

## Wiki 2 Markdown
 * 파일제목 URL decoding
 * 링크 URL decoding
 * 이미지 폴더 제목 URL decoding
 * 문자열 encoding 변경 (파일 제목, 내용) : iso-8859-1 -> UTF-8

```
/* 파일의 현재 인코딩 타입 확인*/
file -bi fileName
```

 * Wiki 2 Mardkdown
 * 모든 문서에 Jekyll header 추가
  - "전 페이지" 를 category로 변경
  - 파일명을 title로 변경
  - 작성일자는 고정
 * 모든 문서의 파일명에 날짜 붙여주기 ( 고정적인 날자값 )
 * category 에 맞는 폴더 생성 및 파일 위치 변경 : 첫번째 카테고리로 합의
 * 불필요한 카테고리 폴더 및 파일 제거

# Execute

### References
 * [HTML2MarkdownByUsingPython](https://github.com/aaronsw/html2text)
 * [HTML2MarkdownByUsingNodeJS](https://github.com/domchristie/to-markdown)
 * [Wiki2MarkdownByUsingPython](https://code.google.com/p/support-tools/wiki/WikiToMarkdownTool)
