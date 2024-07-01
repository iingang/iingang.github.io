---
title: "[vSphere/Linux] Vmware vSphere 환경 RHEL 7.9 설치 방법"
date: 2024-07-01 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, vShphere]
typora-root-url: ./
---

# **VMware vSphere 환경 RHEL 7.9 Install Guide**

---

vSphere 로그인을 하고, 생성하려는 폴더 우클릭하여 새 가상 시스템을 클릭한다.

![image-20240701100033454](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701100033454.png)

<br/>



## **vSphere 구성 설정**

---



### **1. 생성 유형 선택**

---

![image-20240701100113112](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701100113112.png)

새 가상 시스템 생성 >  NEXT

<br/>

### **2. 이름 및 폴더 선택**

---

![image-20240701101426862](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701101426862.png)

가상 시스템 이름 지정 > 폴더 선택 > NEXT

<br/>



### **3. 계산 리소스 선택**

---

![image-20240701101517008](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701101517008.png)

선택 후 NEXT

<br/>

### **4. 스토리지 선택**

---

![image-20240701101628031](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701101628031.png)

선택 후 NEXT

<br/>

### **5. 호환성 선택**

---

![image-20240701101708068](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701101708068.png)

가장 높은값(기본값) 선택 후  NEXT

<br/>

### **6. 게스트 운영 체제 선택**

---

![image-20240701101817039](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701101817039.png)

설치 버전 선택 후 NEXT

<br/>

### **7. 하드웨어 사용자 지정**

---

![image-20240701102352393](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701102352393.png)

CPU - 4코어 > 메모리 - 8GB > 하드디스크 - 100GB > 디스크 프로비저닝 - 씬 프로비전 선택

각자 원하는 환경으로 구성하면 됨

<br/>

![image-20240701102543250](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701102543250.png)

VM Network 선택 > 데이터스토어에 있는 RHEL 7.9 dvd.iso 이미지 파일 선택 > 전원을 킬 때 연결 선택 > NEXT

<br>

### **8. 완료 준비**

---

![image-20240701104807274](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701104807274.png)

구성 확인 후, Finish

<br/>

다 만들어지면 다음과 같이 나온다.

![image-20240701103007462](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103007462.png)



![image-20240701103204269](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103204269.png)

시작 버튼 > 웹 콘솔 시작

<br/>

<br/>

## **RHEL 7.9 설치**

---

![image-20240701104850821](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701104850821.png)

웹 콘솔 시작을 눌러서 뜬 화면에서 **Install Red Hat Enterprise Linux 7.9**를 선택하고 Enter를 눌러 계속 진행한다.

<br/>



### **LANGUAGE 선택**

---

![image-20240701104955014](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701104955014.png)

언어 English 선택 > Continue

<br/>

### **DATE & TIME 변경**

---

![image-20240701103500833](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103500833.png)

Asia > Seoul > Done

<br/>



### **INSTALLATION DESTINATION 선택**

---

![image-20240701103751758](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103751758.png)

100GiB 하드디스크 선택 > DONE



### **BEGIN INSTALLATION**

![image-20240701103841936](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103841936.png)

Begin Installation 클릭하여 설치를 시작한다.

<br/>

### **ROOT PASSWORD 설정**

![image-20240701103950130](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103950130.png)

![image-20240701103921807](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701103921807.png)

root 비밀번호 설정 > Done

<br/>

이제 설치 될 때 까지 기다리면 된다.

설치가 완료되면 다음과 같이 성공적으로 설치되었으며, 재부팅 하라는 문구가 나온다.

![image-20240701105218129](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701105218129.png)

Reboot 클릭하여 재부팅하자.

<br/>

![image-20240701105450359](/../assets/img/posts/2024-07-01-vSphere-rhel-install/image-20240701105450359.png)

OS가 시작되고 설정한 root 비밀번호를 이용해 로그인하면 잘 접속되는 것을 볼 수 있다.

<br/>
