---
title: "[WebLogic] Apache rotatelogs 활용하여 웹로직 nohup 로그 일자별 순환 "
date: 2021-11-04 13:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
---


# **Apache rotatelogs 활용**

---



웹로직에서 server log, access log는 일자별로 순환시킬 수 있지만, nohup 로그는 서버 재기동 시에만 순환된다.

이를 아파치 rotatelogs를 활용하여 일자별로 순환시켜보자.



<br/>



## **Linux**

---



```sh
nohup ./bin/startManagedWebLogic.sh ${SERVER_NAME} ${ADMIN_URL} | ${DOMAIN_HOME}/rotatelogs $LOG_DIR/$SERVER_NAME.out.%Y%m%d 86400 +540 2>&1 &

tail -f $LOG_DIR/$SERVER_NAME.out.`date +%Y%m%d`
```



<br/>

<br/>

## **Windows**

---



```bat
start /B %DOMAIN_HOME%\startWebLogic.cmd | %DOMAIN_HOME%\rotatelogs.exe %LOG_DIR%\%SERVER_NAME%.%%Y-%%m-%%d.log 86400 +540 2>&1 &
```



<br/>



보통 rotatelogs를 사용할 경우, logfile.%Y-%m-%d와 같은 형식의 날짜 포맷을 사용하는데, 해당 문자들 사이의 %기호 때문에 변수로 인식하여 정상적으로 저장되지 않는다.

따라서, %지시자를 통해 날짜 로그 포맷을 지정하려면 위와 같이 %를 두개씩 사용하여 설정해야한다. 

<br/>



