---
title: "[WebLogic] boot.properties 정리"
author: Ingyung Park 
date: 2020-11-14 01:30:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

# boot.properties 파일 default 위치

---

**WebLogic 버전 기준**

8.1 이하

```java
${DOMAIN_HOME}/boot.properties
```

<br/>

9 ~ 10.3.1

```java
${DOMAIN_HOME}/servers/${SERVER_NAME}/security/boot.properties
```

→ $DOMAIN_HOME/boot.properties 에 위치시키면 기본 위치로 복사해줌

<br/>

10.3.2 이상

```java
${DOMAIN_HOME}/servers/${SERVER_NAME}/security/boot.properties  
```



<br/>

<br/>

# 사용자 스크립트 boot.properties 자바 옵션

---

```java
-Dweblogic.system.BootIdentityFile=${DOMAIN_HOME}/boot.properties
```

  

<br/>

<br/>

# 계정 관련 오류 날 때 확인해 볼 사항

---

1. 사용자 계정이 웹로직 엔진에 접근 못하는 경우
2. ip 주소가 AdminServer와 일치하는지 확인
3. 기본 스크립트로도 기동 에러가 발생 할 경우, config 디렉토리 경로의 config.lok 파일, configCache 폴더 지우기  

  <br/>

<br/>

# 계정 초기화 방법

---



1. **웹로직 서버 중지**

	모든 서버를 중지합니다. 
	
	<br/>
	
	

2. **servers 디렉토리 백업**

	$DOMAIN_HOME/servers 디렉토리를 **servers_bak** 으로 파일명을 변경해주시기 바랍니다.  
	
	<br/>


3. **웹로직 계정 변경**

	$DOMAIN_HOME/security 경로의 DefaultAuthenticatorInit.ldift 파일을 **DefaultAuthenticatorInit.ldift_bak** 으로 변경해주시기 바랍니다.

 

	변경 후, 해당 경로에서 관리자 권한으로 cmd 창을 열어주시기 바랍니다. (윈도우 일 때)
	
	아래 명령어로 웹로직 계정을 변경해주시기 바랍니다.

 



```java
${JAVA_HOME}/bin/java -classpath ${ORACLE_HOME}/wlserver/server/lib/weblogic.jar weblogic.security.utils.AdminAccount [웹로직 콘솔ID] [웹로직 콘솔PWD] .
```

**<mark style="background-color: #fff5b1"> → 맨 뒤에 띄어쓰고 . 꼭 붙일 것!</mark>**



<br/>


4. **boot.properties 파일 수정**

  A. boot.properties 파일은 웹로직 기동 시 Admin User를 확인하는데 사용합니다.

  	i. boot.properties 파일 기본 위치 : ${DOMAIN_HOME}/servers/AdminServer/security/boot.properties
  	
  	ii. boot.properties 파일 커스텀 위치 : 
  웹로직 기동 시 사용하는 스크립트에 "-Dweblogic.system.BootIdentityFile"옵션으로 위치가 지정되어 있습니다.

  B. 기본 위치 혹은 커스텀 위치에 있는 boot.properties 파일의 내용을 모두 지우고, 위에서 확인 및 변경한 username, password를 입력합니다.  


<br/>

- **<mark style='background-color: #dcffe4'>boot.properties 파일 생성</mark>**

```java
username=웹로직 콘솔ID
password=웹로직 콘솔PWD
```

 

