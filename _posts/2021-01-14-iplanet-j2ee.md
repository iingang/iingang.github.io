---
title: "[iPlanet] HTTP2020: cannot find template j2ee"
date: 2021-01-14 23:00:00 +0900
categories: [WEB,iPlanet]
tags: [iplanet,Web]
---



# **SYMPTOMS**

---

웹서버 url 호출을 할 때, was가 아닌 web에 있는 jsp 파일을 찾으면서 정상적으로 페이지 호출이 되지 않는 현상

<br/>

로그를 보니 다음과 같은 에러 발생

```shell
process-uri-objects reports: HTTP2020: cannot find template j2ee
```



<br/>

<br/>

# **CAUSE**

---



mime type 설정이 제대로 동작하려면 iPlanet에서 JAVA를 비활성화해야 함

그렇지 않으면 iPlanet 이  `*.jsp`로 끝나는 모든 요청을 처리하려고 시도하고 DocRoot 하위에서 리소스를 찾지 못하므로 404 오류를 반환

<br/>

<br/>

# **SOLUTION**

---

**[인스턴스명]-obj.conf** 파일을 보면 기본적으로 j2ee 사용 활성화가 되어있는데, 이 부분을 주석처리 혹은 삭제하면 됨

<br/>

```shell
<Object name="default">
NameTrans fn="ntrans-j2ee" name="j2ee"
...
</Object>

<Object name="j2ee">
Service fn="service-j2ee"
</Object>
```

<br/>

위 설정을 마치고 웹서버 재기동을 하면 적용 완료!



<br/>