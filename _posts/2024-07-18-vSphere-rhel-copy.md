---
title: "[vSphere/Linux] Vmware vSphere에서 생성한 VM 복제 및 디스크 용량 변경 방법"
date: 2024-07-18 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, vShphere]
typora-root-url: ./
---



## **vSphere VM 복제하기**

---

복제하고자 하는 VM 마우스 우클릭하여 **[복제] > [가상 시스템으로 복제]** 를 누른다.

![image-20240718134605931](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718134605931.png)

<br/>

원하는 **가상 시스템 이름**과 **경로**를 선택한다.

![image-20240718134716561](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718134716561.png)

<br/>

다음을 눌러 진행한다.



![image-20240718134732866](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718134732866.png)

<br/>

복사 대상 VM이 씬 프로비전을 선택했기 때문에 동일하게 가려면 **소스와 동일한 형식**을 선택하면 된다. 

![image-20240718134805012](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718134805012.png)





<br/>



다음을 누르고 완료 버튼을 누르면 복제가 끝난다.

![image-20240718134820917](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718134820917.png)



<br/>



## **설정 편집 (디스크 용량 변경)**

---

복제한 VM은 기존 VM보다 하드 디스크 용량을 적게 사용하고 싶다.

이 경우, 우측 마우스 클릭으로 [설정 편집] 메뉴에 들어간다.

![image-20240718135224758](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135224758.png)



<br/>

디스크 용량을 기존보다 적게 설정하려고 하니 오류가 발생한다.

![image-20240718135202229](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135202229.png)

<br/>

하드 디스크1 우측의 x버튼을 누르고 **데이터스토어에서 파일 삭제**를 체크하여 디스크를 삭제한다.

![image-20240718135433051](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135433051.png)



<br/>

우측 상단 새 디바이스 추가를 눌러 하드디스크를 생성한다.

![image-20240718135516401](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135516401.png)

<br/>

원하는 용량으로 설정하고, 디스크 프로비저닝은 **씬 프로비전(동적 할당)**으로 변경한다.

![image-20240718135627720](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135627720.png)

<br/>

이렇게 설정하고 확인을 누르면 잘 변경되어 적용된 것을 확인할 수 있다.

![image-20240718135751885](/../assets/img/posts/2024-07-18-vSphere-rhel-copy/image-20240718135751885.png)

<br/>
