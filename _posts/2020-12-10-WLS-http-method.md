---
title: "[WebLogic] 웹로직 HTTP method 제한 방법"
date: 2020-12-10 22:30:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
---



# **SOLUTION**

---



웹로직 자체에서 HTTP method 를 제한하는 방법은 없습니다.

> 참고 문서 : How To Disable the HTTP methods other than GET and POST (such as PUT, DELETE, etc.) on a WebLogic Server Domain (Doc ID 1076314.1)

<br/>



web이 아닌 was 에서 설정을 해줘야 한다면, 어플리케이션 단에서 메소드를 제한해줘야 합니다.

<br/>

**[application_name]/WEB-INF/web.xml**



```xml
...
<security-constraint>
  <web-resource-collection>
    <web-resource-name>mytest</web-resource-name>
    <url-pattern>/*</url-pattern>
    <http-method>PUT</http-method>
    <http-method>DELETE</http-method>
  </web-resource-collection>
</security-constraint>
...
```





<br/>