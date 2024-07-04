---
title: "[Grafana] 그라파나 대시보드 권한 설정 (특정 User 제한)"
date: 2024-07-04 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Dashboard, Enterprise, Permission]
typora-root-url: ./
---

## **Grafana Dashboard Permissions Settings**

---

### **1. Grafana Admin 로그인**

---

그라파나 관리자로 로그인 합니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/image-20240704163333057.png)
<br/>

### **2. Dashboard Settings**

---

권한 설정을 원하는 Dashboard에서 우측 상단의 대시보드 설정 버튼을 클릭합니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002.gif)

<br/>

### **3. Permissions 메뉴 클릭**

---

권한 메뉴로 들어갑니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720078691057-2.gif)



<br/>

### **4. Add a Permission 클릭**

---

버튼을 눌러 권한 설정 추가를 할 수 있는데, **<span style='background-color:#fff5b1'>User / Team / Role</span>** 기준으로 선택할 수 있으며, 권한은 3가지 **<span style='background-color:#fff5b1'>Viewer(보기), Edit(편집), Admin(관리자)</span>** 모드로 선택할 수 있습니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720078808833-4.gif)

특정 User 및 권한 설정한 후, Save를 누르면 권한이 추가된 것을 볼 수 있습니다.ㄴ

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720078985992-6.gif)

<br/>

### **5. 해당 User로 대시보드 확인**

---

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720078993024-8.gif)

모든 권한이 있으면 edit 등의 권한도 보여야 하지만, View(보기) 권한만 있는 것이 확인됩니다.

<br/>

### **6. Limiting a Specific User Dashboard (특정 사용자 대시보드 제한)**

---

Grafana는 기본적으로 부모 폴더의 권한을 상속받는 구조로 권한이 적용되기 때문에,  특정 User가 해당 대시보드에 접근하지 않게 하려면 추가적인 설정이 필요합니다.

단순히 User에 대하여 view 설정만 필요하다면 5. 까지의 설정으로 충분하며, **만약 다른 대시보드에서 볼 수 없는 설정**을 원하시면 아래 설정을 **추가**해 주셔야 합니다.

<br/>

#### **6.1. Dashboards 메뉴에서 New folder 생성**

---

접근 제한을 원하는 대시보드를 생성한 폴더 아래에 생성합니다.

Go to folder를 눌러 메뉴 설정을 합니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720079564937-10.gif)

<br/>

#### **6.2. <span style="color: tomato">Viewer Role 삭제</span> 및 <span style="color: tomato">특정 User 추가</span>**

---

폴더의 Permissions 설정에서 기존에 있던 **Viewer Role을 삭제**하고, **해당 폴더 내의 대시보드 권한**이 필요한 **특정 User을 추가**한다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720080036815-12.gif)

위 설정을 해주는 이유는 다음과 같습니다.

먼저 Viewer Role을 삭제하면, 일반 User는 모두 기본적으로 Viewer 역할로 생성되기 때문에 관리자를 제외한 일반 User는 해당 폴더 내에 있는 대시보드에 접근할 수 없습니다.

또한, 특정 User를 추가하는 이유는 특정 User에 대해서만 대시보드를 볼 수 있게 하기 위해서 입니다. (나머지 User는 대시보드 보기 제한됨)

<br/>

User의 Viewer 설정을 **[Administration] > [Users]** 메뉴에서 확인할 수 있습니다.

![img](/../assets/img/posts/2024-07-04-Grafana-Dashboard-Permission/clip_image002-1720080200340-14.gif)

<br/>

여러 User 별로 대시보드를 따로 관리하고 싶다면, 상황에 따라 위와 같이 디렉토리 별로 Permissions 설정을 다르게 구성하면 됩니다.

<br/>

## **References**

---

<https://grafana.com/docs/grafana/latest/administration/user-management/manage-dashboard-permissions/>

<br/>
