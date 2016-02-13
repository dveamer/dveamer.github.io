---
layout: post
title:  "Tools On Ubuntu"
date:   2015-07-04 12:00:00
categories: Ubuntu-Tools
tags: Sublime-Text Remote-Desktop Gedit Tools 
---

Ubuntu에서 활용하는 툴들과 유용한 기능에 대해서 기록해나갈 예정입니다.

<!--more-->

# Gedit
![Gedit](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Gedit-logo-clean.svg/650px-Gedit-logo-clean.svg.png)

( 이미지 출처 : [https://upload.wikimedia.org](https://upload.wikimedia.org) )

## EUC-KR 설정
Windows 에서 작성했던 문서를 열어보니 한글이 깨집니다.  
dconf-editor 를 이용해서 gedit 의 encoding 설정을 수정해야합니다.

dconf-tools 가 설치되어있지 않다면 설치하세요.
~~~
sudo apt-get install dconf-tools
~~~

dconf-editor 를 실행합니다.

~~~
dconf-editor
~~~

좌측 탐색기(?)에서 org > gnome > gedit > proferences > encodings 을 선택합니다.  
auto-detcted 의 값에 'UHC' 를 추가합니다.

![Gedit encoding](/post_img/Ubuntu/gedit_encoding.png)

UHC(Unified Hangul Code) 인코딩 방식은 CP949 라고도 불리며 EUC-KR 인코딩방식에다가 8,822 글자가 추가된 방식입니다.  


# Sublime-Text 2
![Sublime-Text2](https://encrypted-tbn3.gstatic.com/images?q=tbn:ANd9GcQKm1UUFXw0H8iOslGxR4lXcJqkbJuKe_yKXT9r7-GJ6_sAwiNt)

( 이미지 출처 : [https://encrypted-tbn3.gstatic.com](https://encrypted-tbn3.gstatic.com) )

## Weak point
 * No Hangul

### Reference
 * https://opentutorials.org/module/406/3610

## Setting Markdown

### Reference
 * https://github.com/jonschlinkert/sublime-monokai-extended
 * https://github.com/jonschlinkert/sublime-markdown-extended

# Rmote Desktop

## Reference
 * http://uni2u.meximas.com/?p=229


