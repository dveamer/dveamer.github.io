---
layout: post
title:  "DISQUS on Jekyll"
date:   2015-11-30 16:50:00
categories: Homepage
tags: Jekyll DISQUS
---

DISQUS 는 소셜 댓글 서비스 입니다.  
쇼셜의 종류에 상관없이 특정 계정으로 댓글을 남길 수 있으며 관리가 가능합니다.

Github Page에서 돌아가는 이 블로그는 매순간의 댓글을 저장할 공간이 없습니다.  
하지만 DISQUS 와 연동은 HTMl, javascript 만으로도 가능하므로 댓글 기능을 사용 할 수 있습니다.

post에 댓글 추가, 댓글 조회 기능을 추가할 수 있고  
post-list에 댓글 수 기능을 추가할 수 있습니다.

<!--more-->


DISQUS와 를 연동한 결과를 보시려면 이 글의 최하단으로 내려가시면 볼 수 있습니다.

DISQUS에 대한 자세한 설명은 DISQUS 홈페이지 혹은 References 를 참고해주시기 바랍니다.

# Weak point
 * 로딩시간이 생각보다 오래걸립니다.
 * 홈페이지 도메인이 바뀌면 모든 댓글을 조회 못할 수도 있습니다.
   - domain 값을 고정값으로 전달하면 해결 가능합니다.
 * 게시글의 url이 바뀌면 해당 글의 댓글을 조회 못할 수도 있습니다.
   - domain과 identifier를 제공해서 해결 가능합니다.

# Warnning
 * 소스수정 사항은 해당 블로그를 기준으로 진행합니다.
 * 즉, Jekyll-Pithy 외 다른 Theme 의 경우 수정대상이 되는 파일, 폴더 명이 다를 수 있습니다.
 * Jekyll2 외에도 HTML을 수정가능한 모든 종류의 소셜에 적용 가능합니다.

# Comments
 * 게시글에 댓글 작성, 조회에 대한 기능을 추가합니다.

## Features

### Reading & writting comments
 * post의 최 하단에 아래와 같은 댓글기능이 추가 됩니다.

  ![comments sample](/images/post_img/DISQUS/comments.png)

### Print identifier
 * 새로운 comments를 작성하게 되면 DISQUS 서비스로 내용이 전달됩니다.
   특정 Post를 열어보면 DISQUS 서비스로부터 해당 post에 대해 작성되었던 기존 comments들을 불러옵니다.
   이 과정에서 post와 comments들간의 연결고리가 필요한데 보통 post의 url이나 identifier을 연결고리의 key값로 사용합니다.
 * 여기서 문제점은 아래 같은 상황에서 post와 comments간의 연결고리가 끊기고 못 불러올 수 있다는 점 입니다.
   - 홈페이지 도메인 변경
   - post url 변경
   - identifier 변경
 * 저는 고정된 key 값을 사용하는 방법으로 문제를 해결할 예정입니다.
   - site.url 이 변경되더라도 site.disqus_domain은 변경하지 않음으로써 키값으로 계속 사용가능합니다.
   - post에 identifier를 세팅해주면 post url이 변경되더라도 comments를 잘 찾아줍니다. 
   - 매번 identifier를 세팅해주기는 번거롭고 post url이 변경될 경우에만 기존 identifier를 세팅하면 됩니다. 즉, 카테고리, 파일명, 작성날짜, 제목과 같은 meta정보를 수정시에만 identifier를 세팅해주면 됩니다.
   - identifier는 글제목으로해도 되지만 날짜와 제목 10글자의 hashcode값으로 진행했습니다.
 * identifier 확인
   - 손가락 표시를 클릭하면 출력됩니다.

  ![comments identifier](/images/post_img/DISQUS/commentsIdentifier1.png)

  ![comments identifier](/images/post_img/DISQUS/commentsIdentifier2.png)

 * identifier 세팅

~~~
---
layout: post
title:  "DISQUS on Jekyll2"
date:   2015-11-30 16:50:00
categories: Hompage
tags: Jekyll DISQUS
identifier: 201512011177213790
---
~~~

## How to do it
### DISQUS 서비스 가입
 * [DISQUS 서비스](https://disqus.com)에 가입하고 홈페이지를 등록합니다.

### 소스 수정사항
 * /_layouts/post.html 최하단에 아래 내용 추가
  - {, } 기호를 [, ] 로 변환 후 사용하시길 바랍니다.

~~~html
    <div id="disqus_thread"></div>
    <script type="text/javascript">
        var identifier = (function()[
            return getIdentifier('[[ page.identifier ]]', '[[ page.date | date: "%Y%m%d" ]]', '[[ page.title ]]' );
        ])();

        /* * * CONFIGURATION VARIABLES * * */
        var disqus_shortname = '[[ site.disqus_comment_username ]]';
        
        var disqus_config = function () [
            this.page.url = '[[ site.disqus_domain ]][[ page.url | prepend: site.baseurl ]]';
            this.page.identifier = identifier;
        ];

        /* * * DON'T EDIT BELOW THIS LINE * * */
        (function() [
            var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
        ])();

        // set a button checking identifier 
        $(document).ready(function()[
            $("#identifier").bind("click", function()[
                $(this).html(identifier);
            ]);
        ]);
    </script>
    <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
~~~

 * /_saas/_layout.scss 최하단에 아래 내용 추가

~~~scss
    .subtitle-header { 
      padding: 10px 15px;
      color: #fff;
      background-color: #428bca;
      border-color: #428bca;
    }

    .subtitle {
      font-weight:bolder; 
      color:#fff;
    }
~~~

 * /_config.yml 에 추가 ( disqus_shortname 수정 필요 )

~~~
    disqus_domain: "xxxxx.github.io" # your domain
    disqus_comment_username: disqus_shortname # your disqus shortname
~~~

 * /scripts/custom.js 파일 생성 후 아래 내용 추가

~~~js
String.prototype.hashCode = function() {
  var hash = 0, i, chr, len;
  if (this.length === 0) return hash;
  for (i = 0, len = this.length; i < len; i++) {
    chr   = this.charCodeAt(i);
    hash  = ((hash << 5) - hash) + chr;
    hash |= 0; // Convert to 32bit integer
  }
  return hash;
};

var getIdentifier = function(staticIdentifier, date, title) {
    if( staticIdentifier != null ) {
        if( staticIdentifier instanceof String && staticIdentifier.trim() != '' ){
            return staticIdentifier;
        }
    }

    var partOfTitle = (title + '12345678901234567890').substring(0,20);
    var result = ''.concat(date).concat(partOfTitle.hashCode());
    return result;
};
~~~

 * /_layouts/post.html 임의의 위치에 아래 태그 추가

~~~html
    <span id="identifier">☛</span>
~~~

# Comment counts
 * 포스트와 포스트 목록에 각 포스트별 댓글 개수를 출력합니다.
 * /index.html 에 추가 
  - {, } 기호를 [, ] 로 변환 후 사용하시길 바랍니다.

~~~html
  <span class="post-meta">[[ post.date | date: "%b %-d, %Y %H:%m" ]]</span> 

  <!-- 추가 내용 -->
  <span class="post-meta">[[ post.date | date: "%b %-d, %Y %H:%m" ]]</span>
  <span class="post-meta">&nbsp;&nbsp;|&nbsp;&nbsp;</span>
  <span class="post-meta disqus-comment-count" 
    [% if post.identifier.size > 0 %] data-disqus-identifier="[[ post.identifier ]]" [% endif %]
    data-disqus-url="[[ site.disqus_domain ]][[ post.url | prepend: site.baseurl ]]">0 Comments</span>
  <!-- 추가 내용 -->

  <hr id="line"> 
~~~

 * /_layouts/post.html 에 추가
  - {, } 기호를 [, ] 로 변환 후 사용하시길 바랍니다.

~~~html
  <header class="post-header">
    <h1 class="post-title">[[ page.title ]]</h1>
    <p class="post-meta">[[ page.date | date: "%b %-d, %Y" ]]
      [% if page.author %] • [[ page.author ]][% endif %]
      [% if page.meta %] • [[ page.meta ]][% endif %]

    <!-- 추가 내용 -->
      <span class="post-meta">&nbsp;&nbsp;|&nbsp;&nbsp;</span>
      <span class="post-meta disqus-comment-count" 
        [% if page.identifier.size > 0 %] data-disqus-identifier="[[ page.identifier ]]" [% endif %]
        data-disqus-url="[[ site.disqus_domain ]][[ page.url | prepend: site.baseurl ]]">0 Comments</span>
    <!-- 추가 내용 -->

    </p>
  </header>
~~~

# References
 * [Adding comment count links to your homepage](https://help.disqus.com/customer/portal/articles/565624-adding-comment-count-links-to-your-home-page)
 * [블로그에 소셜 댓글 DISQUS를 달아보자!](http://onasaju.tistory.com/182)
