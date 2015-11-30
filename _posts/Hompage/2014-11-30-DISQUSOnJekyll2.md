---
layout: post
title:  "DISQUS on Jekyll2"
date:   2015-11-30 16:50:00
categories: jekyll
---

DISQUS 는 소셜 댓글 서비스 입니다. 
쇼셜의 종류에 상관없이 특정 계정으로 댓글을 남길 수 있으며 관리가 가능합니다.

Github Page에서 돌아가는 이 블로그는 매순간의 댓글을 저장할 공간이 없습니다.
하지만 DISQUS 와 연동은 HTMl, javascript 만으로도 가능하므로 댓글 기능을 사용 할 수 있습니다.

post에 댓글 추가, 댓글 조회 기능을 추가할 수 있고
post-list에 댓글 수 기능을 추가할 수 있습니다.

<!--more-->

* TOC
{:toc}

DISQUS와 를 연동한 결과를 보시려면 이 글의 최하단으로 내려가시면 볼 수 있습니다.

DISQUS에 대한 자세한 설명은 DISQUS 홈페이지 혹은 References 를 참고해주시기 바랍니다.

# Week Point
 * 로딩시간이 생각보다 오래걸림
 * 홈페이지 도메인이 바뀌면 모든 댓글을 조회 못할 수도 있음
 * 게시글의 url이 바뀌면 해당 글의 댓글을 조회 못할 수도 있음

# Attention Required
 * 소스수정 사항은 해당 블로그를 기준으로 진행합니다.
 * 즉, Jekyll-Pithy 외 다른 Theme 의 경우 수정대상이 되는 파일, 폴더 명이 다를 수 있습니다.
 * Jekyll2 외에도 HTML을 수정가능한 모든 종류의 소셜에 적용 가능합니다.

# Comments
 * 게시글에 댓글 작성, 조회 기능을 추가합니다.
 * 모든 게시글에 적용해야하기 때문에 _layouts 과 같은 공통파일을 수정하게 됩니다.
 * 일단, DISQUS 서비스에 가입하고 홈페이지를 등록합니다.
 * /_layouts/post.html 최하단에 아래 내용 추가

```html 

<div class="subtitle-header">
  <h3 class="subtitle">
    Disqus Social Community
  </h3>
  <small>SNS계정으로 댓글을 달아도 SNS에 글이 남지 않습니다.</small><br>
  <small>SNS 계정이 없으신 분은 이메일 주소 입력으로 글을 남길 수 있으며, 답변글이 달리면 이메일로 알림을 받을 수 있습니다.</small><br>
</div>

<div id="disqus_thread"></div>
<script type="text/javascript">
    /* * * CONFIGURATION VARIABLES * * */
    var disqus_shortname = '[[ site.disqus_comment_username ]]';
    
    var disqus_config = function () {
        this.page.url = '[[ site.url ]][[ page.url | prepend: site.baseurl ]]';
        this.page.identifier = '[[ page.url ]]';
    };

    /* * * DON'T EDIT BELOW THIS LINE * * */
    (function() {
        var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
        dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
        (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>

```

 * /_saas/_layout.scss 최하단에 아래 내용 추가

```css

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

```

 * /_config.yml 에 추가 ( disqus_shortname 수정 필요 )

```

  disqus_comment_username: disqus_shortname

```

# Comment Counts
 * 포스트와 포스트 목록에 각 포스트별 댓글 개수를 출력합니다.
 * /index.html 에 추가 

```html

  <span class="post-meta">[[ post.date | date: "%b %-d, %Y %H:%m" ]]</span> 

  <!-- 추가 내용 -->
  &nbsp;&nbsp;|&nbsp;&nbsp;
  <span class="post-meta disqus-comment-count" data-disqus-url="[[ site.url ]][[ post.url | prepend: site.baseurl ]]">0 Comments</span>
  <!-- 추가 내용 -->

  <hr id="line"> 
```

 * /_layouts/post.html 에 추가

``` html
  <header class="post-header">
    <h1 class="post-title">[[ page.title ]]</h1>
    <p class="post-meta">[[ page.date | date: "%b %-d, %Y" ]]
      [% if page.author %] • [[ page.author ]][% endif %]
      [% if page.meta %] • [[ page.meta ]][% endif %]

    <!-- 추가 내용 -->
    &nbsp;&nbsp;|&nbsp;&nbsp;
    <span class="post-meta disqus-comment-count" data-disqus-url="[[ site.url ]][[ page.url | prepend: site.baseurl ]]">0 Comments</span>
    <!-- 추가 내용 -->

    </p>
  </header>

```

# References
 * [Adding comment count links to your homepage](https://help.disqus.com/customer/portal/articles/565624-adding-comment-count-links-to-your-home-page)
 * [블로그에 소셜 댓글 DISQUS를 달아보자!](http://onasaju.tistory.com/182)
