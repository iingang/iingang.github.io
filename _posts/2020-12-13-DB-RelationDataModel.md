---
title: "[DB 데이터베이스] 관계형 데이터 모델 (Relation Data Model)"
date: 2020-12-13 22:30:00 +0900
categories: [IT, Database]
tags: [IT, DB, DataBase]
typora-root-url: ./
---



# **관계형 데이터 모델 (Relation Data Model)**

---

관계형 데이터 모델은 **2차원 구조의 테이블 형태를 통해 자료를 표현**하는 것을 뜻합니다.

동일한 구조(관계)의 관점에서 모든 데이터를 논리적으로 구성한 것입니다.

높은 데이터 독립성을 제공하는 특징이 있습니다.





<br/>



## **기본 용어**

---

- **`Relation`** : 데이터들을 2차원 테이블의 구조로 저장한 것.
- **`Tuple`** : 릴레이션의 행(row)을 나타냄. 속성 값들의 집합이며 **레코드(Record)**라고도 부름.
- **`Attribute`** : 릴레이션의 열(column)을 나타냄.
- **`Cardinality`** : 행의 개수 (=튜플의 개수)
- **`Degree`** : 열의 개수 (=속성의 개수)



<br/>

![image-20201204003948066](/assets/img/posts/relation.png)



<br/>

## **도메인 (Domain)**

---

1. 각 속성(Attributes)에서 나타날 수 있는 모든 값들의 집합이다.

2. 도메인은 **원자값(더 이상 쪼개질 수 없는 속성)**을 가진다.

3. 프로그래밍 언어의 데이터 타입과 유사하다.

4. 동일한 도메인을 여러 속성에서 사용 가능하다.

5. 복합 및 다중 값을 허용하지 않는다.

   > 두 속성의 도메인이 같다 = 두 속성의 데이터 타입과 길이가 같다

   <br/>



## **키 (Key)**

----

### **Super key = 슈퍼키** 



한 릴레이션에서 특정 튜플을 고유하게 식별 할 수 있는 한 개 이상의 속성들로 구성된 키

튜플을 식별하는데 필요하지 않은 속성을 포함할 수도 있다.

모든 튜플에 대해 유일성은 만족하지만, 최소성은 만족시키지 못한다.

{: .prompt-tip }

> [학생] 릴레이션에서 (학번, 주민등록번호), (학번, 주민등록번호, 이름) 등이 슈퍼키이다.

<br/>

### **Candidate key = 후보키**

각 튜플을 식별할 수 있는 최소한의 속성 집합이다.

모든 Relation에는 한개 이상의 후보키가 존재한다.

둘 이상의 속성으로 구성될 수도 있다. (이 경우 복합키(Composite key))

모든 튜플에 대해 **유일성, 최소성 만족**한다.

{: .prompt-tip }

> [학생] 릴레이션에서 학번 또는 주민등록번호는 유일성과 최소성을 만족하므로 후보키이다.
>
> [수강] 릴레이션에서는 (학번, 과목명)으로 조합해야 유일성, 최소성을 만족시키기에 (학번, 과목명)으로 조합된 것이 후보키이며, 2개 이상의 필드를 조합하였기 때문에 복합키라고 한다.

<br/>

### **Primary key = 기본키**

후보키들 중에서 특별히 선정된 **유일한 키**

기본키로 지정된 속성은 동일한 값을 중복하여 저장할 수 없다.

{: .prompt-tip }

> [학생] 릴레이션에서 후보키인 학번이나 주민등록번호 중에서 한 가지를 선택하여 기본키로 설정할 수 있다.

<br/>

### **Alternate key = 대체키**

후보키 중에서 선정된 **기본키를 제외한 나머지 후보키**를 의미한다.

{: .prompt-tip }

> [학생] 릴레이션에서 학번이 기본키라면, 주민등록번호는 대체키이다.

<br/>

### **Foreign key = 외래키**

**다른 릴레이션의 기본키를 참조**하는 속성 또는 속성들의 집합을 의미한다.

릴레이션 간의 관계를 표현할 때 사용한다.

{: .prompt-tip }

> [수강] 릴레이션의 학번은 [학생] 릴레이션의 기본키인 학번을 참조하고 있으므로, [수강] 릴레이션에서 학번은 외래키가 된다.

<br/>