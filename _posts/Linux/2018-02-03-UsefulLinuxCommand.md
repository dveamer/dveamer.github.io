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
$ find {directory_path} -mtime -1 -ls

// sample
$ find . -mtime -1 -ls
195710    4 drwxrwxrwx   1 dveamer     dveamer         4096  2월  3 16:17 .
 31734    4 drwxrwxrwx   1 dveamer     dveamer         4096  2월  3 13:56 ./BackEnd
272650   16 -rwxrwxrwx   1 dveamer     dveamer        12892  2월  3 10:48 ./BackEnd/2016-12-08-InstallNginxTomcat.md
294301    8 -rwxrwxrwx   1 dveamer     dveamer         7605  2월  3 13:56 ./BackEnd/2018-02-03-CompilingInstallationOnLinux.md
294300   12 -rwxrwxrwx   1 dveamer     dveamer         8637  2월  3 13:56 ./BackEnd/2018-02-03-InstallApache.md
185721    4 drwxrwxrwx   1 dveamer     dveamer         4096  2월  3 16:37 ./Linux
272215    8 -rwxrwxrwx   1 dveamer     dveamer         5270  2월  3 16:31 ./Linux/2016-01-12-ProcessStatus.md
272434    4 -rwxrwxrwx   1 dveamer     dveamer         2155  2월  3 16:37 ./Linux/2018-02-03-UsefulLinuxCommand.md
190478    4 drwxrwxrwx   1 dveamer     dveamer         4096  2월  3 13:57 ./Programming
294299    8 -rwxrwxrwx   1 dveamer     dveamer         5108  2월  3 13:56 ./Programming/2015-01-01-TelegramCli.md
195724    4 drwxrwxrwx   1 dveamer     dveamer         4096  2월  3 16:24 ./Ubuntu
~~~

-mtime 옵션의 파라미터 값으로 숫자를 넣게 되는데 1일전의 경우 -1을 입력하면 됩니다.
find 에 -ls 옵션을 빼면 찾은 파일들의 파일명만 출력됩니다.  

## 최근 접속 계정 이력

~~~terminal
$ last
dveamer  pts/6        :0               Sat Feb  3 16:33   still logged in   
dveamer  pts/1        :0               Sat Feb  3 07:39   still logged in   
dveamer  :0           :0               Sat Feb  3 07:38   still logged in   
reboot   system boot  4.4.0-111-generi Sat Feb  3 07:38 - 16:37  (08:59) 
~~~

## alias

~~~terminal
$ alias
alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'
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
$ netstat -nap | grep {port_number}
//OR
$ netstat -nap | grep {proccss_name}
// OR
$ netstat -nap | grep {pid}

//sample
$ netstat -nap | grep firefox
tcp        0      0 192.168.0.4:45620       52.40.210.31:443        ESTABLISHED 8348/firefox    
tcp        0      0 192.168.0.4:37094       162.247.242.21:443      ESTABLISHED 8348/firefox    
tcp        0      0 192.168.0.4:37320       74.125.204.189:443      ESTABLISHED 8348/firefox    
tcp        0      0 192.168.0.4:39454       104.244.42.65:443       ESTABLISHED 8348/firefox    
tcp        0      0 192.168.0.4:33400       104.74.233.222:443      ESTABLISHED 8348/firefox    
tcp        0      0 192.168.0.4:33016       104.74.233.222:443      ESTABLISHED 8348/firefox 
~~~

netstat 옵션에 -p 를 넣게 되면 PID와 프로세스 이름이 함께 출력 출력됩니다.  






