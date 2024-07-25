---
title: "[OHS] Oracle HTTP Server(OHS) 버전 확인 방법"
date: 2022-10-10 09:00:00 +0900
categories: [WEB,OHS]
tags: [Web,OHS]
typora-root-url: ./
---



## **OHS Version 확인하기**

---

OHS 상세버전을 확인하는 방법은 다음과 같다.

<br/>

### **Path 추가**

---

```bash
export LD_LIBRARY_PATH=$OHS_HOME/ohs/lib:$OHS_HOME/lib:$OHS_HOME/oracle_common/lib
```

<br/>

### **버전 확인 명령어 실행**

---

**`$OHS_HOME/ohs/bin/httpd -version`**

```bash
Server version: Oracle-HTTP-Server-12.2.1.4.0/2.4.39 (Unix)
Server built:   Sep 12 2019 19:01:37
```



<br/>

OHS 12c의 경우, **version.txt** 파일에서도 확인 가능하다.

**`cat $OHS_HOME/ohs/bin/version.txt`**

```bash
Oracle HTTP Server
APACHE_12.2.1.4.0_LINUX.X64_190912.1848
apache_12.2.1.4.0
e4d05e795394b8223acd0b2789659d975242a40c
```



<br/>
