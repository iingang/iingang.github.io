---
title: "[WebLogic] boot.properties 정리 / 웹로직 계정 초기화 방법"
date: 2020-11-14 01:30:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic]
---

# **boot.properties 파일 default 위치**

---

### **WebLogic Version 기준**

<br/>

#### **8.1 이하**

```shell
${DOMAIN_HOME}/boot.properties
```

<br/>

#### **9 ~ 10.3.1**

```sh
${DOMAIN_HOME}/servers/${SERVER_NAME}/security/boot.properties
```

→ $DOMAIN_HOME/boot.properties 에 위치시키면 기본 위치로 복사해줌

<br/>

#### **10.3.2 이상**

```sh
${DOMAIN_HOME}/servers/${SERVER_NAME}/security/boot.properties  
```



<br/>

<br/>

# **사용자 스크립트 boot.properties 자바 옵션**

---

```sh
-Dweblogic.system.BootIdentityFile=${DOMAIN_HOME}/boot.properties
```

  

<br/>

<br/>

# **계정 관련 오류 날 때 확인해 볼 사항**

---

1. 사용자 계정이 웹로직 엔진에 접근 못하는지 확인

2. ip 주소가 AdminServer와 일치하는지 확인

3. 기본 스크립트로 기동 되는지 확인 
   
   ```shell
   ${DOMAIN_HOME}/bin/startWebLogic.sh
   ```
   
   - 기동 안 될 경우, config 디렉토리 경로의 config.lok 파일, configCache 폴더 지우고 다시 기동해보기

  <br/>

<br/>

# **계정 초기화 방법**

---

위의 내용을 확인하였는데, 이상이 없는데도 계정 오류가 발생한다면 계정 초기화를 해줍니다.

방법은 아래와 같습니다.

<br/>

## **1. 웹로직 서버 중지**

---



모든 서버를 중지합니다. 

<br/>



## **2. servers 디렉토리 백업**

---



**경로 이동**

```shell
cd $DOMAIN_HOME/servers 
```

<br/>

**파일 백업**

```shell
mv servers servers_bak
```



<br/>

## **3. 웹로직 계정 변경**

---



**경로 이동**

```shell
cd $DOMAIN_HOME/security
```

<br/>

**파일 백업**

```shell
mv DefaultAuthenticatorInit.ldift DefaultAuthenticatorInit.ldift_bak
```



<br/>

**계정 변경**


```sh
${JAVA_HOME}/bin/java -classpath ${ORACLE_HOME}/wlserver/server/lib/weblogic.jar weblogic.security.utils.AdminAccount [웹로직 콘솔ID] [웹로직 콘솔PWD] .
```

> 윈도우의 경우, 관리자 권한으로 cmd 창을 열고 명령어를 입력해주셔야 합니다.

**<mark style="background-color: #fff5b1"> → 맨 뒤에 띄어쓰고 . 꼭 붙일 것!</mark>**



<br/>

## **4. boot.properties 파일 수정**

---



boot.properties 파일은 웹로직 기동 시 Admin User를 확인하는데 사용합니다.

기본 위치는 아래와같습니다.

```shell
${DOMAIN_HOME}/servers/AdminServer/security/boot.properties
```

<br/>

만약 옵션으로 지정하여 사용하고 있다면, 웹로직 기동 스크립트에 **-Dweblogic.system.BootIdentityFile** 옵션으로 파일 위치를 확인하시면 됩니다.

<br/>



계정을 변경하였기 때문에 기존 boot.properties 파일의 내용을 모두 지우고, 위에서 변경한 **`username`**, **`password`**를 입력합니다.  

<br/>

### **boot.properties 파일 생성**

```sh
username=웹로직 콘솔ID
password=웹로직 콘솔PWD
```

 <br/>

세팅이 완료되었으면 웹로직 서버를 기동해주시면 됩니다.

