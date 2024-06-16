---
title: "[Apache] 아파치 기동 오류 - so 모듈 관련"
date: 2021-01-18 14:30:00 +0900
categories: [WEB,Apache]
tags: [Apache,Web]
---



# **Apache 2.4.41 기동 실패 - so 파일 not found**

---

리눅스에서 so 파일을 못찾는 경우에 대한 내용이라 Apache에 국한된 내용은 아니다. 

아파치 설치 후, 웹로직과 연동하기 위해 **`mod_wl_24.so`** 파일을 넣고 **LD_LIBRARY_PATH** 경로를 다음과 같이 잡아줬다.

> export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:~/modules/mod_wl_24.so"

<br/>

아파치 기동을 하니 그래도 so 모듈을 못 찾는 에러가 발생한다.

~~~shell
$ ./apachectl start
Syntax error on line 23 of /home/apache/conf/extra/httpd-vhosts.conf:
Cannot load modules/mod_wl_24.so into server: libonssys.so: 
cannot open shared object file: No such file or directory
~~~

<br/>

찾아보니 바이너리에 setuid, setgid 등이 설정된 경우 리눅스의 로더는 LD_LIBRARY_PATH 환경 변수 설정을 무시해 버린다고 한다.

이런 경우 ld.so.conf.d 경로에 설정을 해줘야한다.

<br/>

root 권한으로 다음과 같이 실행해보자!

```shell
cd /etc/ld.so.conf.d
echo /home/apache/modules > wlsplugin.conf
ldconfig
```



<br/>

**참고 문서**: Doc ID 2522962.1

<br/>