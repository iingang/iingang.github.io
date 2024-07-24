---
title: "[iPlanet] mime.types - json 설정 방법 (컨텐츠 타입)"
date: 2022-10-04 09:00:00 +0900
categories: [WEB,iPlanet]
tags: [iplanet,Web]
typora-root-url: ./
---



## **MIME Type 설정 추가하기**

---

TestApp 애플리케이션 경로의 파일을 호출할 때, **`application/json`** 형식으로 설정하는 방법은 다음과 같다.

<br/>

**`mime.types`** 파일에 JSON 타입을 추가한다.

```bash
type=application/json     exts=json
```

이 설정을 통해 JSON 파일의 MIME 타입을 **`application/json`**으로 지정한다.

<br/>

**`[instance명]-obj.conf`** 파일에 JSON 타입을 추가한다. 

이 파일에서 <span style="color: tomato">**Object Name, 경로(Path), IP, Port 등을 수정**</span>이 필요하다.

```bash
ex) 
<Object name="weblogic" ppath="/TestApp">
ObjectType fn="force-type" type="application/json"
Service fn="wl-proxy" WebLogicHost="ip" WebLogicPort="port"
</Object>
```

{: .prompt-info}

> `name="weblogic"`: 객체 이름
>
> `ppath="/TestApp"`: 애플리케이션 경로
>
> `ObjectType fn="force-type" type="application/json"`: MIME 타입을 `application/json`으로 강제 설정
>
> `Service fn="wl-proxy" WebLogicHost="ip" WebLogicPort="port"`: WebLogic 서버의 호스트와 포트를 설정

<br/>

## **References**

---

<https://docs.oracle.com/cd/E19146-01/821-1827/abvbc/index.html>

<https://docs.oracle.com/cd/E19146-01/821-1827/abumi/index.html>

