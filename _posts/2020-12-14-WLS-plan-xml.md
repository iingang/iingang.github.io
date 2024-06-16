---
title: "[WebLogic] 웹로직 Plan.xml 제거 방법"
author: Ingyung Park
date: 2020-12-14 12:30:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS,application]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **Plan.xml**

---

배치된 어플리케이션에 대해 WebLogic Console 에서 변경작업을 하게 되면, Plan.xml 이라는 파일이 생성됩니다.

<br />

**[배치] > [Application_Name] > 구성 > 일반** 메뉴로 가면 아래 내용을 볼 수 있는데, 콘솔상에서 바꾸지 말고 직접 weblogic.xml 파라미터 값을 변경하여 사용하는 것을 권장합니다.



![image-20201214123707062](/assets/img/posts/plan.png)





<br/>



## **제거 방법**

---

**$DOMAIN_HOME/config/config.xml** 파일에서 아래 2줄을 제거해주시면 됩니다.

```xml
<plan-dir xsi:nil=”true”></plan-dir>
<plan-path>…..</plan-path>
```

<br/>

config.xml 파일이 수정되기 때문에 AdminServer를 재기동 해주셔야 합니다.

이 후, console 화면에서 배치 계획에 Plan.xml에 대한 내용이 없으면 정상적으로 적용된 것입니다.



<br/>