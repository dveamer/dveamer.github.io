---
layout: post
title:  "유용한 Linux 명령어"
date:   2018-02-03 00:00:00
categories: Linux
tags: Command 
---

유용하게 사용하는 Linux 명령어를 기록해봅니다.  

<!--more-->

## 문자열 바꾸기

~~~terminal
$ grep -rl "before text" * | xargs sed -i 's/before text/after text/g'
~~~

일괄적으로 변경되니 조심해서 사용하세요.  

## n 일 전에 변경된 파일 검색

~~~terminal
$ find /directory_path -mtime -1 -ls
~~~

-mtime 옵션의 파라미터 값으로 숫자를 넣게 되는데 1일전의 경우 -1을 입력하면 됩니다.
find 에 -ls 옵션을 빼면 찾은 파일들의 파일명만 출력됩니다.  

## 최근 접속 계정 이력

~~~terminal
$ last
~~~

## alias

~~~terminal
$ alias
~~~

현재 설정된 alias 리스트가 출력됩니다.  

### 유용한 alias 샘플

~~~bash
alias tl='cd ${TOMCAT_LOG_PATH}'
alias tlog='tail -f ${TOMCAT_LOG_PATH}/tomcat.out.${date '+%Y-%m-%d')'
~~~

로그파일 외에도 소스파일, 설정파일, 실행파일 들이 위치한 path도 cd alias를 걸어두면 편리합니다.  

### alias 설정방법

```/.bash_profile```에 입력 후 ```source``` 명령어로 재등록해주면 됩니다.  

~~~terminal
$ vi ~/.bash_profile

$ source ~/.bash_profile
~~~

### process, port 상태 확인

~~~terminal
$ netstat -nap | grep proccss_name

OR 

$ netstat -nap | grep port_number
~~~

netstat 옵션에 -p 를 넣게 되면 PID와 프로세스 이름이 함께 출력 출력됩니다.  






