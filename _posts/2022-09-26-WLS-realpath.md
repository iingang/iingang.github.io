---
title: "[WebLogic] 웹로직 어플리케이션 getRealPath() return값이 NULL"
date: 2022-09-26 18:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
typora-root-url: ./
---



## **현상**

---

웹로직 WAR 아카이브로 배포 시, RealPath를 찾지 못해 NullPointerException가 발생할 수 있다.



<br/>

## **해결 방안**

---

아래 2가지 방법 중 하나로 적용하면 된다.

1. **weblogic.xml 파일 수정**

   ```xml
   <weblogic-web-app>
       <container-descriptor>
           <show-archived-real-path-enabled>true</show-archived-real-path-enabled>
       </container-descriptor>   
   </weblogic-web-app>
   ```

   <br/>

2. **웹로직 콘솔 수정**

   [웹로직 콘솔]  > [도메인] > [구성] > [웹 어플리케이션]

   ![image-20240620212344902](/../assets/img/posts/2022-09-26-WLS-realpath/image-20240620212344902.png)

   <br/>

   **<span style='background-color:#fff5b1'>아카이브된 실제 경로 사용</span> 체크**

   ![image-20240620212354788](/../assets/img/posts/2022-09-26-WLS-realpath/image-20240620212354788.png)



<br/>



## **References**

---

[https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/wbapp/weblogic_xml.html#GUID-ED3EC026-31F6-436F-8EE8-1B5CB4DC937A](https://docs.oracle.com/en/middleware/standalone/weblogic-server/14.1.1.0/wbapp/weblogic_xml.html#GUID-ED3EC026-31F6-436F-8EE8-1B5CB4DC937A)
