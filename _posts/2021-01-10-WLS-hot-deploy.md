---
title: "[WebLogic] 웹로직 hot deploy 방법"
date: 2021-01-10 09:30:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
---



# **WebLogic Application 동적 반영**

---

웹로직 서버 재기동없이 어플리케이션이 실행 중인 상태에서 파일을 배포하고 적용하는 기능을 <span style='background-color:#fff5b1'>**Hot Deploy**</span>라고 한다.



## **weblogic.xml 설정**

```xml
<weblogic-web-app>

    <jsp-descriptor>
        <page-check-seconds>1</page-check-seconds> // jsp 페이지를 1초 간격으로 check 및 reload
    </jsp-descriptor>

    <container-descriptor>
        <servlet-reload-check-secs>1</servlet-reload-check-secs> // WEB-INF/classes 대상으로 1초 간격으로 check 및 reload
        <resource-reload-check-secs>1</resource-reload-check-secs> // 리소스 파일 대상으로 1초 간격으로 check 및 reload
    </container-descriptor>

</weblogic-web-app>
```

<br/>

{: .prompt-tip }
> **-1**: false
>
> **0**: 항상
>
> **1**~: 해당 초 간격으로 reload



<br/>

## **References**

---

[https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/wbapp/weblogic_xml.html](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/wbapp/weblogic_xml.html)