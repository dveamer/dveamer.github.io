---
layout: post
title:  "Bundler를 이용한 Jekyll 3.0 업그레이드"
date:   2016-02-04 23:00:00
categories: Homepage
tags: Jekyll Github-Pages
---

![Bundler](http://ruby-korea.github.io/bundler-site/images/gembundler.png)  
( 이미지 출처 : [http://ruby-korea.github.io/bundler-site/](http://ruby-korea.github.io/bundler-site/) )

>번들러는 필요한 정확한 gem과 버전을 추적하고 설치하여 루비 프로젝트를 위한 일관된 환경을 제공합니다. 
번들러는 의존성 지옥에서 벗어나게 하고, 필요한 gem이 개발, 스테이징, 프로덕션에 있는지 확인해 줍니다.
> ( 출처 : [http://ruby-korea.github.io/bundler-site/](http://ruby-korea.github.io/bundler-site/) )

Bundler는 미리 입력되어있는 의존성에 따라 필요한 프로그램들을 자동으로 설치해줍니다.

아래와 같은 간단한 의존성 기입만으로도 Github-Pages와 동일한 환경을 구성할 수 있습니다.

~~~
source 'https://rubygems.org/'

gem 'github-pages'
~~~

<!--more-->

# Bundler 설치

~~~
gem install bundler
~~~

# Jekyll 3.0 설치 

## Gemfile 생성

홈페이지 / 블로그 폴더 최상단에 Gemfile 이라는 이름의 파일을 만들고 아래와 같이 입력합니다.

```
source 'https://rubygems.org/'

gem 'github-pages'
```

https://rubygems.org/ 에 등록되어있는 github-pages 관련 잼을 이용하라는 내용으로 보여집니다.

## Github-Pages 관련 Bundle 설치

Gemfile을 위치시킨 홈페이지 폴더 최상단에서 아래와 같이 입력합니다.

```
bundle install
```

# Jekyll 실행

Bundler에 의해 설치된 의존성 프로그램들을 이용하기 위해서는 아래와 같이 실행시켜야 합니다.

```
bundle exec jekyll serve --watch
```

# 단점

Github-Pages 관련 잼을 설치하면서 함께 설치/이용되는 프로그램이 무려 52개나 됩니다.

일단 뭔가 쓸데없는 것이 많이 깔리는 느낌이 들어서 기분이 안 좋습니다.  
Github-Pages와 너무 동일한 환경을 맞추는 것은 너무 오버라는 생각도 들지만 그보다 더 큰 문제점이 있습니다.

Jekyll 3.0에서는 사용불가하다는 Textile, Redcarpet 등이 깔리는 것이 눈에 보입니다.

현재 Github-Pages는 어중간한 상태입니다. Jekyll 3.0로 새단장을 했지만 Jekyll 2.0 엔진을 이용할 때 지원하던  
Textile, Redcarpet 등이 2016.04.30 까지는 여전히 사용가능한 상태입니다.

2016.05.01부터 Github-Pages 는 Kramdown, Rouge 만 지원하고 Redcarpet, Pygments 등은 지원이 중단됩니다.
즉, Bundler를 이용해서 현재의 Github-Pages와 동일한 환경을 맞춘다고해도  
그것은 2016.05.01 을 대비한 환경세팅이라고 보기는 좀 어려운 것 같습니다.

[Github Pages의 Jekyll 3.0 업그레이드](/homepage/GithubPagesUseJekyll3.html) 을 통해서 그 때를 대비해 제가 진행한 작업을 설명드리겠습니다.

## 설치 된 프로그램 52개 리스트

```
Installing RedCloth 4.2.9 with native extensions
Installing i18n 0.7.0
Installing json 1.8.3 with native extensions
Installing minitest 5.8.4
Installing thread_safe 0.3.5
Installing addressable 2.3.8
Installing coffee-script-source 1.10.0
Installing execjs 2.6.0
Using colorator 0.1
Using ffi 1.9.10
Installing multipart-post 2.0.0
Installing gemoji 2.1.0
Installing net-dns 0.8.0
Installing public_suffix 1.5.3
Using sass 3.4.21
Using rb-fsevent 0.9.7
Using kramdown 1.9.0
Using liquid 3.0.6
Using mercenary 0.3.5
Using rouge 1.10.1
Using safe_yaml 1.0.4
Installing jekyll-feed 0.3.1
Installing mini_portile2 2.0.0
Installing jekyll-paginate 1.1.0
Installing jekyll-sitemap 0.10.0
Installing rdiscount 2.1.8 with native extensions
Installing redcarpet 3.3.3 with native extensions
Installing terminal-table 1.5.2
Using bundler 1.11.2
Installing jekyll-textile-converter 0.1.0
Installing tzinfo 1.2.2
Installing coffee-script 2.4.1
Installing ethon 0.8.1
Using rb-inotify 0.9.5
Installing faraday 0.9.2
Installing jekyll-sass-converter 1.3.0
Installing nokogiri 1.6.7.2 with native extensions
Installing activesupport 4.2.5.1
Installing jekyll-coffeescript 1.0.1
Installing typhoeus 0.8.0
Using listen 3.0.5
Installing sawyer 0.6.0
Installing html-pipeline 2.3.0
Installing github-pages-health-check 0.6.0
Using jekyll-watch 1.3.1
Installing octokit 4.2.0
Installing jekyll 3.0.2
Installing jekyll-gist 1.4.0
Installing jekyll-mentions 1.0.0
Installing jekyll-redirect-from 0.9.1
Installing jekyll-seo-tag 0.1.4
Installing jemoji 0.5.1
Installing github-pages 45
```

# References
  * https://riseshia.github.io/2016/02/02/upgrade-to-jekyll-3-0.html
  * http://ruby-korea.github.io/bundler-site/
