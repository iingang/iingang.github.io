---
title: "[WebLogic] 웹로직 콘솔 /management 경로 막기"
author: Ingyung Park
date: 2021-03-07 16:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **웹로직 콘솔 /management 경로 막기**

---

<br/>

웹로직 콘솔 ip:port/management 로 접속 시 다음과 같은 화면이 뜬다.



![1](/assets/img/posts/restful1.png)

<br/>



이 경로의 접속을 차단하려면 웹로직 콘솔 설정을 변경하면 된다.



**[도메인명] > [구성] > [일반] > [고급]** 메뉴에서 

![restful2](/assets/img/posts/restful2.png)

<br/>

아래와 같이 **`RESTful 관리 서비스 사용을 체크해제`** 하면된다.

![restful3](/assets/img/posts/restful3.png)





<br/>

AdminServer 및 Managed Server 재기동이 필요하다.

재기동 후, 접근해보면 404 에러나면서 접속되지 않는다.

![restful3](/assets/img/posts/restful4.png)



<br/>