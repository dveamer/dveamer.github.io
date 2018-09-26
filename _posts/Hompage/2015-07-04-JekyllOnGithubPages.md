---
layout: post
title:  "Github-Pages 에 Jekyll 설치하기"
date:   2015-07-04 12:00:00
lastmod : 2016-11-06 12:00:00
categories: Homepage
tags: Homepage Jekyll Github-Pages
---

![Jekyll Logo](http://jekyllrb.com/img/logo-2x.png 'Jekyll Logo'){:class="imgTitle"}  
( 이미지 출처 : [http://jekyllrb.com](http://jekyllrb.com) )  

현재 보고있는 블로그는 Jekyll 2 로 만들어졌습니다.

Markdown 형식으로 작성한 문서를 Jekyll 가 정적인 HTML 형태로 변환해줍니다.  
그리고 그 변환된 HTML을 Github-Page에 올려서 지금 이렇게 볼 수 있는 것입니다.  

좀 더 정확히 설명하자면,   
Github에다가 Markdown 형식의 문서를 올리면 Github가 Jekyll를 이용해서 정적인 HTML로 변환해주고   
우리는 Github-Page에서 브라우저로 볼 수 있게 됩니다.

  * 2016.02.02 이후로 Github Pages 의 Jekyll 엔진이 3.0 으로 업그레이드 되어 변경사항이 생겼습니다.
     - [Github Pages의 Jekyll 3.0 업그레이드](/homepage/GithubPagesUseJekyll3.html)
     - [Bundler를 이용한 Jekyll 3.0 업그레이드](/homepage/UpgradeToJekyll3.0WithBundler.html)
<!--more-->

# Install Jekyll 2 on Ubuntu 14.04 LTS
 * Ruby, NodeJs 설치

~~~console
$ sudo apt-get install ruby ruby-dev make gcc nodejs
~~~

 * Jekyll2 설치

~~~console
$ sudo gem install jekyll --no-rdoc --no-ri
~~~

 * 확인

~~~console
$ jekyll -v
~~~

#### References
 * http://michaelchelen.net/81fa/install-jekyll-2-ubuntu-14-04/

# Create a blog on local computer
 * 기본 블로그 생성

~~~console
$ jekyll new my-awesome-site
~~~

 * Jekyll server 실행

~~~console
$ cd my-awesome-site
$ jekyll serve
~~~

 * 서버기동 확인 (local computer)
  - Open the site(http://localhost:4000) on your browser
  ![JekyllInitialPage](/images/post_img/Jekyll2/JekyllInitialPage.png 'JekyllInitialPage')

#### References
 * http://michaelchelen.net/81fa/install-jekyll-2-ubuntu-14-04/

# Create a blog with theme on Github-Page
 테마를 제공받을 수 있기 때문에 사이트를 백지에서부터 꾸며나갈 필요는 없습니다.  
 테마를 공개해주신 분들에게 감사의 마음을 가져봅시다.

 Github-Page를 이용하면 html 호스팅을 무료로 쉽게 사용가능하며  
 Github가 Jekyll를 지원하기 때문에 Github 에 접속해서 블로그 글을 추가, 수정 할 수도 있습니다.
 
## Github-Page 생성

  * 테마 선택
    - [Jekyll Themes](http://jekyllthemes.org/) 에서 다양한 샘플 확인 후 맘에 드는 테마 선택
    - 해당 테마의 소스가 Github로 공유되어있는지 확인

  * 해당 테마의 repository를 Fork 하기
    - 해당 테마의 repository에 접근해서 Fork 버튼 클릭
    - Fork 하게 되면 해당 테마의 reopository가 본인의 repositories로 복사됨

  * Repository 이름 변경
    - repository의 setting 클릭  
    - username.github.io 로 repository 이름 변경  
      ![Change Repository Name](http://ilmol.com/assets/img/blog/2015reponame.png)  
      ( 이미지 출처 : [Jekyll,Git 을 몰라도 무료 Github Pages 즐기기](https://ilmol.com/2015/01/Jekyll,Git-%EC%9D%84-%EB%AA%B0%EB%9D%BC%EB%8F%84-%EB%AC%B4%EB%A3%8C-Github-Pages-%EC%A6%90%EA%B8%B0%EA%B8%B0.html) )  
      
    - 반드시 본인의 Github username과 동일하게 변경해줘야 Github-Page 기능을 사용가능

  * 웹브라우저에서 https://username.github.io 접속해서 생성여부 확인

## 로컬에서 관리하기

  * Github 에서 로컬로 clone

~~~console
$ mkdir username.github.io.git
$ cd username.github.io.git
$ git clone https://github.com/username/username.github.io.git .
~~~

  * 글 수정, 추가 후 로컬 저장소에 반영하기
    - 저는 RabbitVCS Git 이라는 툴을 사용합니다.

~~~ console
$ git add.
$ git commit -m "commit message" ./*
~~~

## Github Pages 로 올리기 

  * origin 정보 저장 (최초 1회)

~~~console
 # 추가
$ git remote add origin https://username@github.com/username/username.github.io.git

 # 등록여부 확인
$ git remote -v

 # 필요시 삭제 (후 재등록)
$ git remote remove origin
~~~

  * 수정사항에 대해서 Github로 올리기 

~~~console
$ git push -u origin master
~~~

#### References
 * Jekyll Template Site http://jekyllthemes.org/
 * [How to use GitHub Page](http://ilmol.com/2015/01/Jekyll,Git%20%EC%9D%84%20%EB%AA%B0%EB%9D%BC%EB%8F%84%20%EB%AC%B4%EB%A3%8C%20Github%20Pages%20%EC%A6%90%EA%B8%B0%EA%B8%B0.html)
 * Choosed theme (MIT License) http://jekyllthemes.org/themes/pithy/
 * 내가 적용한 theme source https://github.com/smallmuou/Jekyll-Pithy
 * [Getting Started With GitHub Pages](http://guides.github.com/features/pages)

# Warnnings & Tips
 * 글 추가시 파일명은 반드시 yyyy-mm-dd-title.md 형식으로 작성해야합니다. 그렇지 않으면 jekyll 이 인식하지 못 합니다.
 * 수정, 추가시에 Github로 바로 올리지 마시고 local에서 서버를 띄워서 확인 후 올리시면 작업이 좀 더 수월합니다. 
 * 깊게는 아니더라도 github 사용법에 대해서 간단히 공부해보시고 사용하시길 권합니다.
 * .gitignore 을 이용해서 작성 중인 글을 관리 가능합니다.
   - 특정 폴더를 gitignore 대상에 추가합니다. ( 예: _posts/NotOpen )
   - 작성중인 글은 NotOpen 디렉토리에서 작성하고 완성되면 관련 NotOpen 이 아닌 다른 폴더로 옮깁니다.
   - jekyll server를 띄우면 로컬에서는 해당 글이 보입니다. 
   - git add, commit 시에는 대상에서 제외되므로 git push 시에도 Github로 올라가지 않습니다. 
   - 다만, 로컬 저장소와 Github 저장소에 저장되지 않은 상태이니 실수로 지우거나 잘 못 수정하게 되면 복원 할 수 없습니다.
 * .gitignore 에 *~ 와 *.bak 을 추가하세요.

# Configuration
  * /_config.yml 에 추가

~~~
  # Build settings
  markdown: redcarpet
  redcarpet:
    extensions: ["no_intra_emphasis", "tables", "autolink", "fenced_code_blocks", "strikethrough", "hard_wrap"]

  # highlighter: pygments
  permalink: none
~~~


# Content list
  * 모든 post 상단에 목차를 추가한다.
  * markdown 이 제공하는 방법이 있긴한데 markdown을 변경할 때마다 모든 글을 수정해야하는 문제가 있다. 그래서 script 로 진행하는 방법을 선택한다.
  * /_layouts/post.html 에 추가

~~~html
<div class="post">
  <br>
  <header class="post-header">
    ....
  </header>

  <!-- 추가 코드 -->
  <div id="toc" hidden>
    <H3 class="index">== I N D E X ==</H3>
  </div>
  <br/><br/>
  <!-- 추가 코드 -->

  <article class="post-content">
    ....
  </article>
</div>


<!-- 추가 코드 -->
<script type="text/javascript">
 $(document).ready(function(){
    var tag = null;
    $("h1,h2,h3,h4,h5,h6").not(".subtitle").not(".post-title").not(".index").each(function(i,item){
      tag = $(item).get(0).localName;
      $(item).attr("id","content_"+i);
      $("#toc").append('<a class="new'+tag+'" href="#content_'+i+'">'+$(this).text()+'</a></br>');
    });

    if(tag !=null ){
      $(".newh1").css("margin-left",0);
      $(".newh2").css("margin-left",20);
      $(".newh3").css("margin-left",40);
      $(".newh4").css("margin-left",60);
      $(".newh5").css("margin-left",80);
      $(".newh6").css("margin-left",100);

      $("#toc").attr('hidden', false);
    }
 });
</script>
<!-- 추가 코드 -->
~~~

# Comments
 * DISQUS 라는 소셜 댓글 서비스를 활용해서 댓글 기능을 추가할 수 있습니다.
 * 그 외에도 댓글 수, 조회 수 출력 기능을 추가할 수 있습니다.
 * 자세한 내용은 (DISQUS on Jekyll2)[/jekyll/2015/12/01/DISQUSOnJekyll2.html] 를 참고하세요.
 
# View Counts
 * DISQUS 혹은 Google Analyticator 중 하나를 활용해야 할 것 같아서 고민 중입니다.
 * https://developers.google.com/analytics/devguides/reporting/?csw=1
 * https://wordpress.org/plugins/google-analyticator/
 * http://jhshi.me/2013/11/10/page-view-plugin-for-octopress/

# References
 * https://github.com/saltfactory/saltfactory.github.io/blob/master/_config.yml
 * [Jekyll 문법](http://jekyll.pygmeeweb.com/tests/)

