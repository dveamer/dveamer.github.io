---
layout: diary
title:  "예전 홈페이지 백업"
date:   2019-05-06 00:00:00
categories: Diary
tags: Diary Backup
hidden: true
published: true
---

학생 때 제로보드와 Moniwiki를 가지고 운영하던 개인 홈페이지를 미러링 작업을 통해 HTML로 백업을 진행했다.  

오랜만에 홈페이지를 둘러보니 이미지 링크가 깨진 문서들이 많았고  
심지어 방문자들이 작성하는 "freeboard"와 내 일정은 기록한 "schedule" 게시판은 DB 테이블이 날아갔다는 에러메시지도 떴다.  

<!--more-->

아쉽지만 그래도 내 생각들을 주로 남아있는 "그림일기" 게시판과 이것저것 많은 기록을 해둔 wiki는 살아있는게 어딘가 라는 심정으로 백업을 진행했다.  

진행하면서 알게 된 사실인데 다행히도 freeboard와 schedule 게시판도 완전히 날아간 것은 아니었고 테이블명이 엉뚱한 것으로 바뀌어있었다.  
테이블을 원상태로 복귀시켜 보니 몇달 전에 해당 게시판에 스팸성 댓글이 무수히 달려있는 것을 확인할 수 있었다.  
호스팅 관리자가 스팸 댓글을 확인 후 게시판이 관리도 안되고 있는 것으로 보이니 테이블명을 바꿔놓는 (좀 말이 안되는) 조치를 해둔 것으로 예상된다.  
아마도 "사용 중인 게시판이라면 연락이 오겠지..?"라는 생각이 아니었을까 싶다.  

어쨋든 다행히 모든 게시판의 내용은 복구를 했지만 모든 게시판에 대해서 백업을 진행하지 않고 freeboard, 그림일기 그리고 wiki에 대해서만 진행을 했다.  
wget 명령어를 이용해서 미러링을 하려고 했으나 쉽지만은 않았다. 게시글은 554개인데 미러링 된 게시글은 2만개가 넘는 등 다양한 상황을 마주하게 됐다.  
그 과정에 대해서는 다른 글로 기록을 남길 예정이다.  

아무튼 백업은 진행이 완료됐다. 완벽하게 모든 글이 되진 않았고 깨진 글들이 없는지 확인 작업도 필요하지만 그래도 마음이 많이 편해졌다.  

<br>

앞으로는 어떻게 관리해야할까..  
지금 글을 작성하고 있는 이 블로그는 정적페이지로 관리하고 있는데 이 방법은 문제가 없을까..? 라는 고민을 하게 된다.  

기록은 중요하다..
하지만 그것이 계속해서 늘어나는 짐이 되는 것은 피해야한다.  

문제 점은 없는지 생각해보자.  


