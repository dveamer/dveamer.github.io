---
layout: post
title:  "Github Pages의 Jekyll3.0 업그레이드"
date:   2016-02-02 23:00:00
categories: Homepage
tags: Jekyll Github-Pages
---

오늘 아침 Github 트위터 계정을 통해 Github Pages의 새로운 소식을 들었습니다.

[GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)

2016.02.02부로 Github Pages에서 사용하는 Jekyll 엔진을 버전 2.0에서 3.0 으로 교체했다고 하네요.

속도가 빨라지고 단순해졌다.. 라면서 여러가지 좋은 점들을 적어놓은 것 같긴한데 별로 중요해 보이지 않습니다.

중요해 보이는 것들은 변경사항들..

오늘부터 syntax highlighter 를 Rouge 만으로 사용이 제한되며
2016.05.01 부터는 Markdown는 Kramdown 만 사용가능하다고 합니다.

그 외에도 변경사항들이 더 있습니다.

Jekyll & Github Pages 구성이라면 평생토록 유지보수 없이 사용이 가능하다고 생각했었는데 아주 큰 착각이었네요 -_-ㅋ

<!--more-->

일단 내 노트북의 Jekyll 버전을 3.0 으로 올리고 
KRamdown 과 Rouge로 설정을 바꿔서 정상적으로 출력되는지 확인해봐야겠습니다.

# 변경사항
  * Kramdown 만 사용가능 : Markdown
    - 2016.05.01 부터
    - Rdiscount, Redcarpet 사용불가
 
  * Rouge 만 사용가능 : Syntax Highlighter
    - 2016.02.01 부터
    - Pygments 사용불가

  * 

# Jekyll 3.0 환경세팅

## Jekyll 2.5.3 제거

[Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)대로 Jekyll 버전을 업데이트를 해봤습니다.

```
sudo gem update jekyll
```

실패합니다. 실패메시지도 제대로 출력되지 않습니다.
인터넷을 찾아보고 이것저것 시도해서 얻은 결론은 Ruby와 Gem의 버전 문제입니다.
Jekyll 3.0을 설치하기 위해서는 Ruby2.0, Ruby 2.0-dev, gem2.0 이상의 버전이 필요합니다.

제 Jekyll 버전이 2.5.3 이었는데.. 만약 이것보다 높은 버전을 쓰고 있다면 
[Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)처럼 진행해보시기 바랍니다.


만약 낮은 버전을 쓰고있으셨다면 Ruby, Gem 의 버전을 먼저 업그레이드 후에 설치가 가능합니다.
근데 재미있는 점이 있습니다. Ruby2.0을 설치하고나면 Jekyll 2.5.3 버전이 삭제가 되지 않습니다. 
즉, 업데이트 실패하게 됩니다. Ruby2.0 으로 올리기 전에 먼저 제거 작업을 진행합니다.

```
sudo gem uninstall jekyll
```

## Ruby 2.0 설치

현재 버전을 확인합니다.

```
ruby -version

result>
ruby 1.9.3p484 (2013-11-22 revision 43786) [x86_64-linux]
```

Ruby 2.0 설치를 진행합니다.

```
sudo apt-get install ruby2.0
sudo apt-get install ruby2.0-dev
sudo ln -sf /usr/bin/ruby2.0 /usr/bin/ruby
```

버전을 확인합니다.

```
ruby -version

result>
ruby 2.0.0p384 (2014-01-12) [x86_64-linux-gnu]
```

## Gem 2.0 설치
Ruby 설치 후 재시도를 해봤지만 동일한 에러메시지가 발생합니다. 
검색을 해보니 Gem도 버전을 2.0으로 올려야한다는 글이 있습니다.

현재 Gem 버전을 확인합니다.

```
gem --version

result>
1.8.23
```

Gem을 업그레이드 합니다.

```
sudo apt-get upgrade gem
sudo ln -sf /usr/bin/gem2.0 /usr/bin/gem
```

버전을 확인합니다.

```
gem --version

result>
2.0.14
```

## Jekyll 3.0 설치

```
sudo gem install jekyll
```

버전을 확인합니다.

```
jekyll -version

result>
jekyll 3.1.1
```

# _config.yml 변경
기존의 redcarpet과 pygments 를 제거했더니 highliter가 다 깨집니다.. ㅜㅡ
어떻게 변경해야하는지는 아직 파악 중입니다.

  * 변경 전

```
  # Build settings
  markdown: redcarpet
  redcarpet:
    extensions: ["no_intra_emphasis", "tables", "autolink", "fenced_code_blocks", "strikethrough", "hard_wrap"]

  # highlighter: pygments
  permalink: none
```

  * 변경 후 

  * Highlighter 관련 자료 : [Rouge Sample](http://rouge.jayferd.us/demo)

# References
  * [GitHub Pages now faster and simpler with Jekyll 3.0](https://github.com/blog/2100-github-pages-now-faster-and-simpler-with-jekyll-3-0)
  * [Jekyll 홈페이지의 업그레이드 가이드](http://jekyllrb.com/docs/upgrading/2-to-3/)
  * [Jekyll Release 소식](https://jekyllrb.com/news/2015/10/26/jekyll-3-0-released/)
  * https://github.com/kersulis/kersulis.github.io/blob/master/_config.yml

