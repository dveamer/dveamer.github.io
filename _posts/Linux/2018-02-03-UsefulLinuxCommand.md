---
layout: post
title:  "유용한 Linux 명령어"
date:   2018-02-03 00:00:00
lastmod: 2020-06-06 00:00:00
categories: Linux
tags: Linux Command 
---

유용하게 사용하는 Linux 명령어를 기록해봅니다.  

<!--more-->

## 문자열 바꾸기

~~~terminal
$ grep -rl "before text" * | xargs sed -i 's/before text/after text/g'
~~~

현재 디렉토리와 하위 디렉토리의 모든 before text를 after text로 변경합니다. 조심해서 사용하세요.  


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
$ vim ~/.bash_profile

$ source ~/.bash_profile
~~~

## process, port 상태 확인

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


## 로그파일에서 특정범위 추출하기

Error 관련 로그를 공유하려고 할때 번거로운 경우가 많습니다.  
예를들어, vim으로 로그파일을 열었는데 에러건 관련 INFO, ERROR 로그들이 내용이 많아서  
한 화면에 안잡히면 마우스로 한번에 드래그가 되지 않아서 복사가 쉽지 않습니다.  

vim에서 클립보드로 직접 복사하는 방법이 있긴한데  
vim 버전, 플러그인 문제로 vim을 새로 설치해야하는 경우가 있습니다.  

vim에서 라인수를 파악하고  
sed로 해당 라인을 추출하는 방법을 알아봅니다.  

vim 에서 ```:set number``` 명령어를 이용해서 추출하려는 로그의 라인수를 확인하세요.  
예를들어 뽑아내고 싶은 메시지가 12303 라인부터 12402 라인까지라면 sed를 아래와 같이 사용하시면 됩니다.  

~~~terminal
$ sed -n '12303,12402p' raw.log > extracted.log
~~~

## 디렉토리 용량 확인


### 현재 디렉토리의 총 용량 확인

~~~terminal
$ du -sh
~~~

### 현재 디렉토리의 구성파일들의 용량 확인

~~~terminal
$ du -h --max-depth=1
~~~

### 특정 용량 이상의 파일 찾기 

~~~terminal
$ ls -al `find . -size +1M`
~~~

위의 방법은 모든 파일을 찾은 다음에 출력되므로 느리고 너무 많은 파일이 발견되면 실패할 때도 있습니다.  
그럴 때는 find에서 제공하는 옵션을 이용해서 처리 가능합니다.  

~~~terminal
$ find . -size +1M -exec ls -al {} \;
~~~

## tail & quit

특정 단어가 나오면 tail을 중단하는 방법입니다.  
CI/CD를 구성할 때 로그 모니터링을 통해 정상기동 유무를 체크 할 경우 사용할 수 있습니다.  

~~~terminal
$ tail -f <filename> | { sed "/<serching word>/ q" ;}
~~~

예를들어서 test.txt 파일을 tail 걸고 EOF라는 단어가 출력되면 tail 이 중단되는 예제입니다.  
터미널 창을 두개를 띄우고 한쪽에서는 test.txt 파일을 수정을 합니다.  

~~~terminal
$ echo 'plain text' >> test.txt
$ echo 'plain text 2' >> test.txt
$ echo 'EOFtext' >> test.txt
$ echo 'test' >> test.txt
~~~

그리고 앞의 수정이 일어나기 전에 다른 터미널 창에서 tail을 걸고 있었다고하면 아래와 같은 결과가 출력됩니다.  

~~~terminal
$ echo '' > test.txt 
$ tail -f test.txt | { sed "/EOF/ q" ;}

plain text
plain text 2
EOFtext
$
~~~

근데 EOFtext가 입력됐을 때 tail이 종료되지 않고 마지막 test가 입력되었을 때 tail이 종료되는 현상을 확인할 수 있습니다.  
EOF가 입력 된 이후 어떤 값이든 한 차례 더 입력되어야지만 tail 프로세스가 완전히 종료되는 것을 주의해야합니다.  

즉, EOF 라는 단어를 끝으로 더 이상 로그를 남기지 않는 시스템에 대해서 EOF로 중단유무를 체크한다면 tail 프로세스가 종료되지 않을 수 있습니다.  

#### References 

  * [stackoverflow : do-a-tail-f-until-matching-a-pattern](https://stackoverflow.com/questions/5024357/do-a-tail-f-until-matching-a-pattern)


