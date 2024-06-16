---
title: "[WebLogic] 웹로직 서버 강제로 종료되는 현상  "
date: 2022-02-27 18:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
typora-root-url: ./
---

# **웹로직 서버 셧다운 현상**

---

<br/>

중지 스크립트를 실행한 것이 아닌데, 자동으로 웹로직 서버가 내려가는 현상 발생

<br/>

[정상 종료 로그]

```
<2022. 2. 21 오후 3시 47분 07초 GMT+09:00> <Info> <Server> <BEA-002640> <A request has been received to force shut down of the server.>
<2022. 2. 21 오후 3시 47분 07초 GMT+09:00> <Info> <Server> <BEA-002638> <Force shutdown was issued remotely from 127.0.0.1>
<2022. 2. 21 오후 3시 47분 07초 GMT+09:00> <Alert> <WebLogicServer> <BEA-000396> <Server shutdown has been requested by weblogicred>
<2022. 2. 21 오후 3시 47분 07초 GMT+09:00> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to FORCE_SUSPENDING>
```

<br/>[강제 종료 로그]

  ```
  <2022. 2. 21 오후 8시 34분 20초 GMT+09:00> <Notice> <WebLogicServer> <BEA-000388> <JVM called WLS shutdown hook. The server will force shutdown now>
  <2022. 2. 21 오후 8시 34분 20초 GMT+09:00> <Alert> <WebLogicServer> <BEA-000396> <Server shutdown has been requested by <WLS Kernel>>
  <2022. 2. 21 오후 8시 34분 20초 GMT+09:00> <Notice> <WebLogicServer> <BEA-000365> <Server state changed to FORCE_SUSPENDING>
  ```

<br/>

위와 같이 **JVM called WLS shutdown hook. The server will force shutdown now** 와 같은 내용이 있으면,

어플리케이션에 System.exit() 메소드를 사용하지 않는지 확인해보자.



<br/>
