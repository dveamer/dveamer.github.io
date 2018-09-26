---
layout: post
title:  "Github Pages의 Jekyll 3.0 업그레이드"
date:   2016-02-02 23:00:00
lastmod: 2016-04-23 00:00:00
categories: Homepage
tags: Homepage Jekyll Github-Pages Kramdown Rouge Upgrade Update Support
---

![Github Pages 의 Jekyll3.0 업그레이드](/images/post_img/Homepage/GithubPagesUseJekyll3.0.png){:class="imgTitle"}  


오늘 아침 Github 트위터 계정을 통해 Github Pages의 새로운 소식을 들었습니다.

[GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)

2016.02.02부로 Github Pages에서 사용하는 Jekyll 엔진을 버전 2.0에서 3.0 으로 교체했다고 하네요.

속도가 빨라지고 단순해졌다.. 라면서 여러가지 좋은 점들을 적어놓은 것 같긴한데 별로 중요해 보이지 않습니다.

중요해 보이는 것들은 변경사항들..

오늘부터 syntax highlighter 는 Rouge 만으로 사용이 제한되며  
2016.05.01 부터는 Markdown는 Kramdown 만 사용가능하다고 합니다.

그 외에도 변경사항들이 더 있습니다.

Jekyll & Github Pages 구성이라면 평생토록 유지보수 없이 사용이 가능하다고 생각했었는데 아주 큰 착각이었네요 -_-ㅋ

<!--more-->

일단 노트북의 Jekyll 버전을 3.0 으로 올리고   
KRamdown 과 Rouge로 설정을 바꾼 후 정상적으로 출력되도록 post들을 수정했습니다.

# Github, Jekyll 정책 변경사항
  * Kramdown 만 사용가능 : Markdown
    - 2016.05.01 부터
    - Rdiscount, Redcarpet 사용불가
 
  * Rouge 만 사용가능 : Syntax Highlighter
    - 2016.02.01 부터
    - Pygments 사용불가

  * Relative permalinks 사용불가
    - Jekyll 3.0에서 지원하지 않음
    - 다행히 저는 절대주소를 사용해왔습니다.

  * Textile 사용불가
    - 2016.02.01 부터

Textile 는 정확히 무엇을 의미하는지 모르겠으나 전 사용하지 않으므로 생략합니다.

# 시작하기 전에

제가 진행한 업그레이드 방법은 살짝 단순무식한 느낌이 있습니다.

Jekyll 3.0을 설치하고 config에 kramdown, rouge를 설정하고 홈페이지를 띄우게 되면 화면이 깨져서 출력됩니다.
저는 기존에 작성한 포스트들의 마크다운 문법을 일부 수정해서 처리했습니다.

이 포스트를 작성 후 몇일 뒤 [Bundler를 이용한 Jekyll 3.0 업그레이드](/homepage/UpgradeToJekyll3.0WithBundler.html) 방법을 알게됐습니다.  
이 방법으로 Jekyll 3.0을 설치하고 실행하게 되면 기존의 마크다운 문법을 수정하지 않고서도 홈페이지가 깨지지 않습니다.

하지만 확실하지 못하다는 느낌이 있습니다.  
왠지 2016.05.01 이후에 화면이 깨지는 것이 아닐까? 라는 의문이 듭니다.  
그 이유는 [Bundler를 이용한 Jekyll 3.0 업그레이드](/homepage/UpgradeToJekyll3.0WithBundler.html) 포스트에서 설명 드리겠습니다.

저는 이 포스트에 기록된 방법으로 마크다운 문법을 수정해둔 상황입니다.

두 포스트를 모두 읽어보시고 선택해서 진행하시기 바랍니다.

제가 생각하는 가장 좋은 방법은 순수하게 Jekyll 3.0을 설치하고  
rouge에 에 대한 옵션들을 설정해서 기존 문법을 그대로 재활용하는 방법일 것 같습니다.

다만 제가 rouge 에 대해서 아는 바가 없습니다....

# Jekyll 3.0 환경세팅

## Jekyll 2.5.3 제거

[Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)대로 Jekyll 버전을 업데이트를 해봤습니다.

~~~console
$ sudo gem update jekyll
~~~

실패합니다. 실패메시지도 제대로 출력되지 않습니다.  
인터넷을 찾아보고 이것저것 시도해서 얻은 결론은 Ruby와 Gem의 버전 문제입니다.  
Jekyll 3.0을 설치하기 위해서는 Ruby2.0, Ruby 2.0-dev, gem2.0 이상의 버전이 필요합니다.

만약 낮은 버전을 쓰고있으셨다면 Ruby, Gem 의 버전을 먼저 업그레이드 후에 설치가 가능합니다.  
근데 재미있는 점이 있습니다. Ruby2.0을 설치하고나면 Jekyll 2.5.3 버전이 삭제가 되지 않습니다.   
즉, 업데이트 실패하게 됩니다. Ruby2.0 으로 올리기 전에 먼저 제거 작업을 진행합니다.

~~~console 
$ sudo gem uninstall jekyll
~~~

그래도 일단 [Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)처럼 진행해보시기 바랍니다. 혹시 성공할지도 모르니까요 ^^

## Ruby 2.0 설치

현재 버전을 확인합니다.

~~~console 
$ ruby -version

result>
ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]
~~~

Ruby 2.0 설치를 진행합니다.

~~~console
$ sudo apt-get install ruby2.0
$ sudo apt-get install ruby2.0-dev
$ sudo ln -sf /usr/bin/ruby2.0 /usr/bin/ruby
~~~

버전을 확인합니다.

~~~console
$ ruby -version

result>
ruby 2.0.0p384 (2014-01-12) [x86_64-linux-gnu]
~~~

## Gem 2.0 설치
Ruby 설치 후 재시도를 해봤지만 동일한 에러메시지가 발생합니다.  
검색을 해보니 Gem도 버전을 2.0으로 올려야한다는 글이 있습니다.

현재 Gem 버전을 확인합니다.

~~~console
$ gem --version

result>
1.8.23
~~~

Gem을 업그레이드 합니다.

~~~console
$ sudo apt-get upgrade gem
$ sudo ln -sf /usr/bin/gem2.0 /usr/bin/gem
~~~

버전을 확인합니다.

~~~console
$ gem --version

result>
2.0.14
~~~

## Jekyll 3 설치

~~~console
$ sudo gem install jekyll
~~~

버전을 확인합니다.

~~~console
$ jekyll -version

result>
jekyll 3.1.1
~~~

# _config.yml 변경

## 마크다운

기존의 redcarpet과 pygments 를 kramdown과 rouge로 변경했더니 highliter와 line break 등 문서가 조금씩 깨집니다.  
저는 아직 글을 많이 올리지 않은 상황 그나마 다행입니다.

  * 변경 전

~~~
  # Build settings
  markdown: redcarpet
  redcarpet:
    extensions: ["no_intra_emphasis", "tables", "autolink", "fenced_code_blocks", "strikethrough", "hard_wrap"]

  # highlighter: pygments
  permalink: none
~~~

  * 변경 후 

~~~
markdown: kramdown

kramdown:
  input: GFM
  syntax_highlighter: rouge
~~~

  * Highlighter 관련 자료 : [Rouge Sample](http://rouge.jayferd.us/demo)

## timezone 문제

Jekyll 3.0 UTC +00 기준으로 작성시각이 설정됩니다.  
Github-Pages 를 이용하는 상황에서 해당 설정을 바꿀수는 없는 것으로 보여집니다.  

다만, 아래와 같이 Config에 timezone을 설정하면  
화면에 출력될 때 UTC +09 기준으로 출력할 수는 있습니다.  

~~~
timezone: '+0900'  

작성시각 : 2016.02.02 23:00 / UTC +00
출력시각 : 2016.02.03 08:00 / UTC +09
~~~

더 어려운 점은  
작성시각이 빌드시각보다 미래일 경우 해당 페이지가 출력되지 않습니다.  
기껏 작성해서 Github-Pages에 올렸지만 빌드과정에서 제외 되기 때문에 페이지가 보이지 않게 됩니다.  
나중에 다른 글을 수정하거나 올려서 재빌드 과정을 거쳐야 페이지가 보이게 됩니다.  

제 홈페이지는 등록시각은 별 의미가 없고 날짜만 출력하기 때문에  
등록시각을 모두 00:00 으로 맞출 예정입니다.  
그러면 오전 9시 이후에 작성하는 글은 모두 정상적으로 빌드 됩니다.  
만약에 새벽이나 오전 9시 이전에 작성하는 글은 전날 날짜로 입력할 예정입니다.  

# posts 변경

툴을 이용해서 폴더 내의 모든 문서를 한꺼번에 변경하시면 편하실 것 같습니다.

## 엔터 -> 더블 스페이스 
기존에는 엔터만 쳐도 다음 라인으로 넘어가도록 세팅되어있었는데 kramdown의 기본 모드에서는 안되는 모양입니다. 대신 표준 마크다운 문법인 스페이스 두번으로 문서들을 변경해줬습니다.

## Highlight for Code Blocks 

~~~
``` 표시를 ~~~ 으로 변경
~~~

## line break 변경

~~~
</br> 을 <br> 로 변경
~~~

# Category 페이지 변경
Jekyll 2.0에서는 site.categories 에서 카테고리 리스트를 뽑으면 그 결과가 전부 소문자로 출력됐었습니다.  
Jekyll 3.0에서는 대소문자가 포스트 작성시 입력한대로 출력되어 보기 좋아졌습니다.  
근데 이유는 모르겠지만 엔터, 스페이스가 섞여들어 있습니다. 그 때문에 카테고리 페이지에서 에러가 발생했습니다.  
Ruby, Jekyll 관련 문법을 제대로 몰라서 검색 좀 했네요.  
strip_newlines와 lstrip, rstrip 으로 엔터와 스페이스를 제거해서 처리했습니다.

# Jekyll 3 와 함께 설치되는 패키지 리스트

제 노트북에 설치 된 gem list를 깨끗하게 모두 제거 후 Jekyll 3.0을 설치해봤습니다.
Jekyll 3.1.1 과 함께 설치되는 프로그램 리스트는 아래와 같습니다. 총 14개 입니다.

~~~
Fetching: safe_yaml-1.0.4.gem (100%)
Successfully installed safe_yaml-1.0.4
Fetching: rouge-1.10.1.gem (100%)
Successfully installed rouge-1.10.1
Fetching: mercenary-0.3.5.gem (100%)
Successfully installed mercenary-0.3.5
Fetching: liquid-3.0.6.gem (100%)
Successfully installed liquid-3.0.6
Fetching: kramdown-1.9.0.gem (100%)
Successfully installed kramdown-1.9.0
Fetching: ffi-1.9.10.gem (100%)
Building native extensions.  This could take a while...
Successfully installed ffi-1.9.10
Fetching: rb-inotify-0.9.6.gem (100%)
Successfully installed rb-inotify-0.9.6
Fetching: rb-fsevent-0.9.7.gem (100%)
Successfully installed rb-fsevent-0.9.7
Fetching: listen-3.0.5.gem (100%)
Successfully installed listen-3.0.5
Fetching: jekyll-watch-1.3.1.gem (100%)
Successfully installed jekyll-watch-1.3.1
Fetching: sass-3.4.21.gem (100%)
Successfully installed sass-3.4.21
Fetching: jekyll-sass-converter-1.4.0.gem (100%)
Successfully installed jekyll-sass-converter-1.4.0
Fetching: colorator-0.1.gem (100%)
Successfully installed colorator-0.1
Fetching: jekyll-3.1.1.gem (100%)
Successfully installed jekyll-3.1.1
Parsing documentation for safe_yaml-1.0.4
Installing ri documentation for safe_yaml-1.0.4
Parsing documentation for rouge-1.10.1
Installing ri documentation for rouge-1.10.1
Parsing documentation for mercenary-0.3.5
Installing ri documentation for mercenary-0.3.5
Parsing documentation for liquid-3.0.6
Installing ri documentation for liquid-3.0.6
Parsing documentation for kramdown-1.9.0
Installing ri documentation for kramdown-1.9.0
Parsing documentation for ffi-1.9.10
Installing ri documentation for ffi-1.9.10
Parsing documentation for rb-inotify-0.9.6
Installing ri documentation for rb-inotify-0.9.6
Parsing documentation for rb-fsevent-0.9.7
Installing ri documentation for rb-fsevent-0.9.7
Parsing documentation for listen-3.0.5
Installing ri documentation for listen-3.0.5
Parsing documentation for jekyll-watch-1.3.1
Installing ri documentation for jekyll-watch-1.3.1
Parsing documentation for sass-3.4.21
Installing ri documentation for sass-3.4.21
Parsing documentation for jekyll-sass-converter-1.4.0
Installing ri documentation for jekyll-sass-converter-1.4.0
Parsing documentation for colorator-0.1
Installing ri documentation for colorator-0.1
Parsing documentation for jekyll-3.1.1
Installing ri documentation for jekyll-3.1.1
14 gems installed
~~~

# References
  * [GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)
  * [Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)
  * [Jekyll Release 소식](https://jekyllrb.com/news/2015/10/26/jekyll-3-0-released/)
  * [Jekyll 문법](http://jekyll.pygmeeweb.com/tests/)
