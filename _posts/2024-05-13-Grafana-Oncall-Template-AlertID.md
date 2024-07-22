---
title: "[Grafana 그라파나] Grafana OnCall Email Template Alert ID 출력"
date: 2024-05-13 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Enterprise, OnCall]
typora-root-url: ./
---

## **그라파나 온콜 템플릿 Alert ID 추출하기**

---

Grafana OnCall에서 알림이 발생하면 이메일로 받으려고 한다.

이메일 템플릿에 그라파나 대시보드에서 볼 수 있는 Alert ID를 불러오기 위한 문법은 다음과 같다. 

**`{% raw %}{{ grafana_oncall_incident_id }}{% endraw %}`**

<img src="/../assets/img/posts/2024-05-13-Grafana-Oncall-Template/image-20240711145355512.png" alt="image-20240711145355512" style="zoom:80%;" />

<br/>

위와 같이 설정해주면, 다음과 같이 **#60** 의 이벤트 번호를 출력할 수 있다.

<img src="/../assets/img/posts/2024-05-13-Grafana-Oncall-Template/image-20240711145441934.png" alt="image-20240711145441934" style="zoom:80%;" />



<br/>
