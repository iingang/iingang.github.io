---
title: "[WebLogic] 웹로직 버전 확인 방법"
date: 2020-12-22 20:40:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS,version]
---



# **WebLogic Version 확인하기**

---

<br/>

## **1. 명령어로 확인**

---

**1.1 환경변수 세팅**

```sh
cd ${DOMAIN_HOME}/bin
```

```sh
. ./setDomainEnv.sh
```

<br/>

**1.2 버전 확인**

```sh
java weblogic.version
```

![image-20201222205250902](/assets/img/posts/config_xml.png)

더 상세하게 보려면 **java weblogic.version `-verbose`** 옵션을 붙여서 입력하시면 됩니다.

<br/>

<br/>

## **2. config.xml 파일 확인**

---

> WebLogic 구버전은 예외

<br/>

**2.1 경로 이동**

```sh
cd ${DOMAIN_HOME}/config
```

<br/>

**2.2 버전 확인**

**config.xml** 파일 상단의 **`<domain-version>`** 태그에서 확인 가능합니다.

<br/>

<br/>

## **3. registry.xml 파일 확인**

---

**3.1 경로 이동**

```sh
cd ${MW_HOME}
```

<br/>

**3.2 버전 확인**

**registry.xml** 파일에서 **`version`**으로 검색하면 확인 가능합니다.

<br/>

<br/>

## **4. WebLogic Admin Console 확인**

---

- 웹로직 11g 이상 부터는 **콘솔 좌측 하단**에서 버전 정보 확인이 가능합니다.
- **[환경] > [서버] > [서버이름] > [모니터링] > [일반]**  탭에서도 WebLogic 버전 확인 할 수 있습니다.

<br/>