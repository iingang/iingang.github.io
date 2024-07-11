---
title: "[Grafana] 그라파나 대시보드 권한 잠금해제(Unlock) 방법"
date: 2024-07-05 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Dashboard, Enterprise, Permission]
typora-root-url: ./
---

## **Grafana Dashboard Unlock Permissions**

---

### **Symptoms**

---

**<span style="color: tomato">대시보드 자체의 권한</span>** 메뉴에서 User에 대한 권한이 Lock 걸려있는데, 이를 삭제하는 버튼이 활성화 되어있지 않다. 

![img](/../assets/img/posts/2024-07-05-Grafana-Dashboard-Permission-Lock/clip_image002-1720659625815-2.gif)

그 이유는 **Grafana 권한 구조는 부모 폴더 권한을 상속받아 적용**되기 때문이다.

<br/>

### **Solutions**

---

대시보드가 속해있는 **<span style="color: tomato">폴더의 권한</span>** 메뉴를 확인하면 다음과 같이 삭제 버튼이 활성화 된 것을 볼 수 있다.

이를 해결하기 위해 **상위 폴더의 권한 설정을 삭제**하면 하위 대시보드 권한에도 잘 반영된다.

![img](/../assets/img/posts/2024-07-05-Grafana-Dashboard-Permission-Lock/clip_image002.gif)

<br/>

만약 User 별로 대시보드 권한을 분리해서 관리하고 싶다면, User 수만큼 폴더를 새로 만들어서 **<span style='background-color:#fff5b1'>상위 폴더 권한(Role - Viewer 제거, 특정User - View 권한 추가)</span>**에 설정하니 잘 동작하는 것을 확인하였다.

<br/>

## **References**

---

<https://grafana.com/docs/grafana/latest/administration/user-management/manage-dashboard-permissions/>

<br/>
