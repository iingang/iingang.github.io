---
title: "[Jennifer] 제니퍼 에이전트 삭제/제거 방법"
date: 2020-12-08 10:00:00 +0900
categories: [APM,Jennifer]
tags: [APM, Jennifer, Agent, Delete]
typora-root-url: ./
---


## **Jennifer Java Agent 삭제 방법**

---

에이전트.conf 파일을 지우면 대시보드에서 에이전트 삭제 버튼이 활성화 된다.

또는 WAS 서버 기동 중지해도 버튼이 활성화 되어 제거 가능하다.

<br/>

둘 다 안되는 경우, conf 파일 지우고, Jennifer View 서버 재기동을 해보자.

아래와 같이 도메인 메뉴에서 stopped instance 자동 제거 체크하고, 일정시간 기다리면 삭제된 것을 볼 수 있다.

![png](/../assets/img/posts/2020-12-08-Jennifer-Agent-Delete/.png)



<br/>

