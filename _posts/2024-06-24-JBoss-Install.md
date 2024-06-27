---
title: "[JBoss] JBoss EAP 7 Install Guide"
date: 2024-06-24 10:00:00 +0900
categories: [WAS,JBoss]
tags: [WAS, JBoss, RedHat, Middleware]
typora-root-url: ./
---


## **JBoss EAP 7.4 설치 방법**

---

{: .prompt-info }
> [테스트환경]
>
> openjdk version "11.0.14.1" 2022-02-08 LTS

### **JBOSS EAP 7.4 에서 지원되는 Java 버전**

<div align="left"><img src="/../assets/img/posts/2024-06-24-JBoss-Install/image-20240627132637384.png" alt="image-20240627132637384" style="zoom:67%;" /></div>



### **설치 파일 압축 해제**

```bash
unzip jboss-eap-7.4.0.zip
```

<br/>

### **jboss-eap7 설치**

```bash
yum groupinstall jboss-eap7
```

<br/>

### **JBoss 설치 프로그램 실행**

```bash
java -jar jboss-eap-7.4.0-installer.jar
```

만약 GUI 액세스가 어렵고, 터미널을 통해서만 접근할 수 있는 경우, 다음과 같이 콘솔모드로 실행할 수 있다.

```bash
java -jar jboss-eap-7.4.0-installer.jar -console

Select language :
0: eng
1: chn
2: deu
3: fra
4: jpn
5: por
6: spa
Please choose [0] :
END USER LICENSE AGREEMENT
JBOSS(r) ENTERPRISE MIDDLEWARE(tm)
...
press 1 to continue, 2 to quit, 3 to redisplay
1
Select the installation path: [/opt/jboss-eap-7.4]
```



<br/>

### **설정 XML 파일 생성**

```bash
java -jar jboss-eap-7.4.0-installer.jar myinstall.xml
```



<br/>

## **References**

---

[책] Red Hat JBoss Enterprise Application Platform Administration I

<br/>

