---
title: "[Jennifer] 제니퍼 temp 디렉토리 관련 에러 발생"
date: 2020-10-27 10:00:00 +0900
categories: [APM,Jennifer]
tags: [APM, Jennifer, error]
typora-root-url: ./
---


## **제니퍼 temp 경로 에러 발생**

---

```bash
2020-10-27 08:50:52.559 [main] WARN  o.e.jetty.webapp.WebAppContext - Failed startup of context o.e.j.w.WebAppContext@9353778{/,null,UNAVAILABLE}{D:\jennifer\server.view\war\jennifer.server.view-5.4.2.3.war}
java.lang.IllegalStateException: Failed to delete temp dir D:\jennifer\server.view\temp\5.4.2.3
```

위와 같은 에러가 발생하면서 모니터링이 안되는 현상이 발생했다.

${JENNIFER_HOME}/server.view/temp 디렉토리를 temp_back으로 mv 시킨 후 제니퍼 서버를 재기동하니깐 문제가 해결되었다.



<br/>

