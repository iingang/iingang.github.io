---
title: "[Jennifer] 제니퍼 HttpIOException 모니터링 제외 방법"
date: 2022-06-24 10:00:00 +0900
categories: [APM,Jennifer]
tags: [APM, Jennifer]
typora-root-url: ./
---


## **HttpIOException 에러 수집 안 되도록 설정**

---

이 에러가 자주 발생해서 X-View에서 아예 수집 안되게 하려면 **에이전트 고급옵션 설정**을 해줘야 한다.

<span style='background-color:#fff5b1'>ignore_http_io_exception</span> 기본값이 false기 때문에 true로 변경해줘야 한다. 

인스턴스 재기동 없이 바로 반영된다.

![image-20240627121945017](/../assets/img/posts/2022-06-24-Jennifer-HttpIOException/image-20240627121945017.png)



<br/>

## **References**

---

JENNIFER5 매뉴얼

<br/>

