---
title: "[Jennifer] 제니퍼 DB Connection 종류"
date: 2022-07-26 10:00:00 +0900
categories: [APM,Jennifer]
tags: [APM, Jennifer, DB]
typora-root-url: ./
---



## **제니퍼 DB Connection 종류**

---



![png](/../assets/img/posts/2022-07-26-DB-Connection/.png)

####  **Active DB connection**

- JAVA 일 때, JMX 설정된 경우, JMX 에서 제공하는 수

- JAVA 일 때, JMX가 설정되지 않은 경우, 실행 중인 SQL 의 수

- .NET 일 때, 실행 중인 SQL의 수

 <br/>

#### **Idle DB connection**

- JAVA 일 때, JMX 설정된 경우, JMX 에서 제공하는 수
- JAVA 일 때, JMX가 설정되지 않은 경우, 살아있는 Connection 객체의 수
- .NET 일 때, 살아있는 Connection 객체의 수

 <br/>

#### **Configured DB connection**

- JAVA 만 수집 가능하며, JMX 설정되어 있을 때만 수집 가능

- Pool 관련 Metric 으로 어플리케이션 서버에 설정한 최대 커넥션 수를 의미

<br/>



JMX 설정이 안되어 있는데 connection pool 별 내용은 DataSource (JNDI) 를 사용하는 경우에 해당 이름 별로 보여 주는 방법을 사용하고 있다. DataSoucre 를 사용해도 JNDI 이름을 구하지 못하는 경우에는 하나로 합쳐서 보인다.



<br/>

## **References**

---

JENNIFER5 매뉴얼

<br/>

