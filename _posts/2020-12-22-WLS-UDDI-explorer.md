---
title: "[WebLogic] 웹로직 UDDI Explorer 비활성화 방법"
date: 2020-12-22 21:50:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
---



# **WebLogic UDDI Explorer disable 방법**

---

<br/>

UDDI가 활성화 되어있는 경우,

http://ip:port/uddiexplorer 를 호출하면 WebLogic UDDI Explorer 페이지를 볼 수 있습니다.

<br/>

배포된 어플리케이션이 해당 기능을 사용하지 않는다면, 보안의 이유로 비활성화 하는 것을 권장합니다.



<br/>

## **1. uddi.* 파일 삭제**

---

**1.1 경로 이동**

```sh
cd ${WL_HOME}/server/lib
```

<br/>

**1.2 uddi 관련 파일 삭제**

> ls -rtl uddi* 명령어로 관련 파일 확인

**`uddi.properties`** **`uddi.war`** **`uddiexplorer.war`**

```sh
rm uddi.properties uddi.war uddiexplorer.war
```

<br/>

백업본을 남기시려면 해당 파일을 ${WL_HOME}/server/lib 디렉토리 영역 외부로 이동시키기 바랍니다.

<br/>

<br/>

## **2. uddi 및 uddiexplorer 캐시 파일 삭제**

---

**2.1 경로 이동**

```sh
cd ${DOMAIN_HOME}/servers/AdminServer/tmp/.internal
```

<br/>

**2.2 파일 삭제**

**`uddi.war`** **`uddiexplorer.war`**

```sh
rm uddi.war uddiexplorer.war
```



<br/>

**2.3 경로 이동**

```sh
cd ${DOMAIN_HOME}/servers/AdminServer/tmp/_WL_internal
```

<br/>

**2.4 디렉토리 삭제**

**`uddi`** **`uddiexplorer`**

```sh
rm -rf uddi uddiexplorer
```



<br/>

<br/>

## **3. 웹로직 서버 재기동**

---

웹로직 서버를 재기동 해주시기 바랍니다.

<br/>

정상적으로 적용이 되었으면, 

```sh
<Warning> <Deployer> <BEA-149617> <Non-critical internal application uddi was not deployed. Error: [Deployer:149158]No application files exist at '<WL_HOME>\server\lib\uddi.war'.>

<Warning> <Deployer> <BEA-149617> <Non-critical internal application uddiexplorer was not deployed. Error: [Deployer:149158]No application files exist at '<WL_HOME>\server\lib\uddiexplorer.war'.>
```



위와 같은 메시지와 함께, UDDI explorer가 비활성화 되었음을 알 수 있습니다.

<br/>

10.3.6 버전의 경우, *WLS PSU 10.3.6.0.190115* 패치로 UDDI 관련 내부 앱이 비활성화되기 때문에 추가적인 조치가 필요하지 않습니다.

<br/>

**[참고 문서] : Oracle Doc ID 1274906.1** 

<br/>