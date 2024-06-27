---
title: "[Jennifer] 제니퍼 X-View 특정 응답시간 미만 모니터링 제외 방법"
date: 2021-02-17 10:00:00 +0900
categories: [APM,Jennifer]
tags: [APM, Jennifer, transaction]
typora-root-url: ./
---

## **응답시간이 1초 미만인 경우, 데이터 저장 or 모니터링 하지 않는 방법**

---

### **실시간 X-View 모니터링 관련**

---

<span style="color: tomato">설정 > 룰 > 실시간 X-View Cache</span>

트랜잭션이 많을 경우, 특정 응답시간 이하의 트랜잭션을 실시간 X-view 차트에서 보지 않게 하기 위한 설정이다.

실시간 X-view 차트에서만 안보일 뿐 저장도 되고 검색도 가능하다.

데이터 서버의 성능대비 트랜잭션이 많아서 데이터 유실이 발생할 경우, 최소 응답시간 1000ms(1초) 설정을 사용하면 도움이 된다.

![png](/../assets/img/posts/2021-02-17-Jennifer-XView/.png)

<br/>

<span style="color: tomato">설정 > 프로파일 > 자동 스택트레이스</span>

자동 스택트레이스에서 설정한 수행시간 임계치를 초과하여, 자동으로 수집된 스택 트레이스 데이터를 분석한다.

설정한 응답시간을 초과한 트랜잭션에 대한 StackTrace 데이터를 확인할 수 있다.

<br/>



### **Jennifer DB 저장 관련**

---

<span style="color: tomato">설정> JENNIFER DB > 프로파일 저장 제한</span>

이 설정은 제니퍼 DB에도 저장하지 않게 하는 설정이다.

이 설정만 하면 실시간 X-View에 트랜잭션은 찍히지만, 프로파일 정보가 나오지 않는다. 

즉, 트랜잭션 유입만 확인 가능하다. 당연히 분석/통계 메뉴에서 검색도 불가능하다.

<br/>

## **References**

---

JENNIFER5 매뉴얼

<br/>

