---
title: "[iPlanet] iPlanet SSL 인증서 등록 방법"
author: Ingyung Park
date: 2020-12-31 00:30:00 +0900
categories: [WEB,iPlanet]
tags: [iplanet,Web]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **iPlanet SSL 인증서 등록**

---

<br/>

## 1. **key3.db(개인키), cert8.db 파일 등록**

---



발급 받은 key3.db 및 cert8.db 인증서 파일을 아래 위치에 넣어줍니다.

```shell
${IPLANET_HOME}/https-[SERVER_NAME]/config
```



<br/>



## **2. 인스턴스 배포**

---



관리 콘솔 접속을 위해 어드민 서버를 실행합니다.

```shell
${IPLANET_HOME}/admin-server/bin/startserv
```

<br/>

[구성] > [서버명] 메뉴 클릭 후, 우측 상단의 **`수정된 인스턴스 구성`** 클릭해줍니다.

**구성 가져오기 및 배포**를 선택하고 확인을 클릭합니다.

![iPlanet](/assets/img/posts/iplanet.png)



<br/>

## **3. 비밀번호 설정**

---



[구성] > [서버명] > [HTTP Listener] 메뉴에서, SSL 포트로 사용할 http-listener를 생성합니다.

생성한 리스너를 클릭하면 SSL 탭이 있는데, 인증서가 정상 등록 되었다면 비밀번호 설정을 할 수 있습니다.

<br/>



## **4. SSL 설정**

---



ssl 인증서 비밀번호를 설정하고 저장하면 SSL 탭 안의 보이지 않던 내용이 아래와 같이 뜹니다.

![iPlanet_SSL](/assets/img/posts/iplanet_ssl.png)

<br/>

SSL 항목 사용가능 체크해주고, 인증서 항목도 선택해줍니다.



암호화 프로토콜은 가장 최신버전만 사용하게끔 선택합니다. (위의 사진은 SSLV3, TLS1.0 둘 다 사용 가능하게 체크됨)

> 테스트 한 버전은 iPlanet 7.0.11 로 TLS 1.0까지만 지원
>
> iPlanet 의 경우, 7.0.21 버전 이상부터 TLS 1.2 를 지원

<br/>



설정이 완료되었으면, https로 정상 서비스 되는지 확인해주세요.

<br/>