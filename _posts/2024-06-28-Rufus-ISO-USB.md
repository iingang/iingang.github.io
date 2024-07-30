---
title: "[Rufus] Rufus를 이용하여 ISO 파일로 USB 부팅디스크 생성 방법"
date: 2024-06-28 09:00:00 +0900
categories: [ETC, Rufus]
tags: [ISO, RUFUS, RHEL]
typora-root-url: ./
---



## **Rufus를 사용해 ISO 파일로 부팅 USB 만드는 방법**

---



### **Rufus Download**

---

<https://rufus.ie/downloads/>

```
rufus-4.5p.exe             2024-05-22 12:10  1.4M  Rufus 4.5 (Portable Version)
```

위의 Portable Version으로 다운로드 받았다.

휴대용 버전과 일반 버전의 차이는 휴대용 버전은 기본적으로 rufus.ini를 생성하는 것 말고 큰 차이는 없는듯하다.

<br/>

### **ISO 이미지 선택 및 USB 부팅디스크 생성**

---

다운로드 받은 Rufus 파일을 실행시면 아래와 같이 나온다.

![image-20240628102601201](/../assets/img/posts/2024-06-28-iso-usb/image-20240628102601201.png) 



<br/>

기존에 만들어둔 RHEL 8.4 버전 kickstart.iso 파일을 선택한다.

![image-20240628103614536](/../assets/img/posts/2024-06-28-iso-usb/image-20240628103614536.png)





**시작**을 누른다.

![image-20240730130609597](/../assets/img/posts/2024-06-28-Rufus-ISO-USB/image-20240730130609597.png)

위와 같은 팝업창이 뜨면 확인을 누르고 넘어간다.

<br/>

![image-20240628104030316](/../assets/img/posts/2024-06-28-iso-usb/image-20240628104030316.png)

시간이 걸리지만 잘 진행되는 것을 볼 수 있다.

완료되고 닫기 누르면 USB 부팅디스크가 잘 만들어진다.