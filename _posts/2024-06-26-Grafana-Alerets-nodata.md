---
title: "[Grafana 그라파나] 이벤트가 No Data(event)일 때, 특정 User에게 알람 전송"
date: 2024-06-26 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Alert, Enterprise, OnCall]
typora-root-url: ./
---

## **No Data일 경우, 특정 User에게 Alerts 설정**

---

### **1. Contact Point 설정**

---

알림을 받고자 하는 특정 user에 대한 **contact point**를 추가한다. 

[Home] > [Alerts & IRM] > [Alerting] > [contact points]

![Untitled](/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/Untitled.png)

<br/>

### **2. Notification Polices 설정**

---

Custom Label (key, value)을 지정하여 알림 정책을 설정한다.

알아보기 쉽게 <span style='background-color:#fff5b1'>**Metric 이름**, **결과**(aws.billing.actual_spend, nodata) </span>를 각각 key, value 값으로 설정하였고, 이를 위에서 만든 contact point와 연결한다.

[Home] > [Alerts & IRM] > [Alerting] > [Notification polices]

![Untitled (1)](/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/Untitled (1).png)

<br/>

### **3. Alerts Rule 설정**

---

원하는 Metric과 조건을 입력하고, no data일 경우 Alerting 및 2.에서 설정한 Custom Label (key, value)을 동일하게 입력한다.

![Untitled (2)](/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/Untitled (2).png)

<img src="/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/Untitled (3).png" alt="Untitled (3)" style="zoom:67%;" />

<br/>

### **4. Rule 설정 완료 및 메일 수신**

---

위와 같이 설정하면, no data일 경우 firing이 발생하며 아래와 같이 메일이 발송된다.

(custom label로 contact point를 정할 수 있다는 점이 알림 설정의 핵심이다.)

![Untitled (4)](/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/Untitled (4).png)

![image-20240628234727344](/../assets/img/posts/2024-06-28-Grafana-Alerets-nodata - 복사본/image-20240628234727344.png)

<br/>
