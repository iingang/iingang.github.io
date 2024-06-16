---
title: "[WebLogic] 웹로직 최신 패치 후, 보안성 경고 알람 disable 방법 "
date: 2022-03-03 18:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
typora-root-url: ./
---


# **웹로직 패치 후 보안 경고 없애는 방법**

---

<br/>

## **현상**

---

웹로직 최신 패치 후, 경고성 문구 및 테이블 보임

![Image](/../assets/img/posts/Image-16484560939021.png)

<br />

![Image](/../assets/img/posts/Image-16484561070472.png)



<br/>

## **해결 방안**

---



### **1. [도메인명] > [보안] > [Warnings] 탭에서 3가지 항목 체크 박스 해제**

좌측 상단의 잠금 및 편집 버튼누르고 아래 3가지 항목 체크박스 해제하자.

![Image](/../assets/img/posts/Image-16484561152813.png)



해제 후 저장하여 변경 내용 및 활성화 버튼을 누르자.



<br/>

### **2. Refresh Warning 버튼 클릭**

위의 경고성 테이블에서 **Refresh Warning** 버튼을 클릭하면 바로 반영된다.

![image-20220328173203158](/../assets/img/posts/image-20220328173203158.png)



<br/>

### **3. 콘솔 상단 경고 문구 해제**

아래와 같이 경고 문구가 사라진 것을 확인 할 수 있다.

![Image](/../assets/img/posts/Image-16484561479864.png)



<br/>
