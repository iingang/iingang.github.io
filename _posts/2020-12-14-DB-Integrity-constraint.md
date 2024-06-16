---
title: "[DB 데이터베이스] 무결성 제약조건 (Integrity Constraint)"
author: Ingyung Park
date: 2020-12-14 19:30:00 +0900
categories: [IT, Database]
tags: [IT, DB, DataBase]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **무결성 (Integrity)이란?**

---

**`무결성`**이란 **데이터의 정확성, 일관성**을 나타냅니다.

데이터에 결함이 없는 상태, 즉 데이터를 정확하고 일관되게 유지하는 것을 의미합니다.

<br/>

<br/>



# **무결성 제약조건 (Integrity Constraint)**

---

**`무결성 제약조건`**이란 <mark style="background-color: #fff5b1">데이터베이스의 정확성, 일관성을 보장하기 위해 저장, 삭제, 수정 등을 제약하기 위한 조건</mark>을 뜻합니다.

주요 목적은 **데이터베이스에 저장된 데이터의 무결성을 보장하고 데이터베이스의 상태를 일관되게 유지하는 것**입니다.



<br/>

## **1. 개체 무결성**

각 릴레이션의 기본키를 구성하는 속성은 **널(NULL) 값이나 중복된 값**을 가질 수 없습니다.

> [학생] 릴레이션에서 '학번'을 기본키로 지정했다면 '학번' 속성은 NULL이 되어서는 안된다.





<br/>

## **2. 참조 무결성**



외래키 값은 NULL이거나 참조하는 릴레이션의 기본키 값과 동일해야 합니다. 

즉, 각 릴레이션은 참조할 수 없는 외래키 값을 가질 수 없습니다.

> [수강] 릴레이션에서 '학번' 속성에는 [학생] 릴레이션의 '학번' 속성에 없는 값은 입력할 수 없다.





<br/>

## **3. 도메인 무결성**

속성들의 값은 정의된 도메인에 속한 값이어야 합니다.

> '성별'이라는 속성에서 '남', '여'를 제외한 데이터는 제한되어야 한다.



<br/>

## **4. 고유 무결성**

특정 속성에 대해 고유한 값을 가지도록 조건이 주어진 경우, 릴레이션의  각 튜플이 가지는 속성 값들은 서로 달라야 합니다.

> [학생] 릴레이션에서 '이름', '나이'는 서로 같은 값을 가질 수 있지만 '학번'의 경우, 각 튜플은 서로 다른 값을 가져야 한다.



<br/>

## **5. NULL 무결성**

릴레이션의 특정 속성 값은 NULL 될 수 없습니다.

> [학생] 릴레이션 정의 시 '과목' 속성에 NULL 값이 올 수 없도록 제한했다면 '과목' 속성에 NULL이 있어서는 안된다. 



<br/>

## **6. 키 무결성**

각 릴레이션은 최소한 한 개 이상의 키가 존재해야 합니다.