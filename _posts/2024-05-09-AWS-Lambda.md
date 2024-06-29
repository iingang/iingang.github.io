---
title: "[AWS SAA] Lambda 람다 "
date: 2024-05-09 09:00:00 +0900
categories: [Cloud,AWS]
tags: [Cloud, AWS, SAA, Lambda]
typora-root-url: ./
---



# **AWS Lambda**

---

- #### **개요**

  - **<span style='background-color:#fff5b1'>서버를 구축 및 프로비저닝 하는 과정을 거치지 않고 코드를 실행</span>**하는 서버리스 서비스
  - 상태를 비저장 및 내구성 방식으로 트리거하여 특정 이벤트에 반응
  - 자동 확장 및 비용 효율적인 장점이 있음
  - 함수는 **최대 <span style='background-color:#fff5b1'>15분</span> 동안 실행**할 수 있음
  - **128MB~10,240MB** 까지 1MB 단위로 **<span style='background-color:#dcffe4'>메모리</span>** 할당 가능 (최대 10GB)
  - **512MB~10,240MB** 사이의 임시 **<span style='background-color:#dcffe4'>스토리지</span>(/tmp)**를 구성할 수 있음

- #### **Lambda SnapStart**

  - 함수 실행 속도 및 성능 가속화 기능으로, 콜드 스타트(시작 지연 시간)를 제거함
  - 함수 **코드를 변경하지 않고**도 **추가 비용 없이** 지연 시간에 민감한 애플리케이션의 **시작 성능을 최대 10배**까지 높일 수 있음

- #### **Lambda 함수의 실행 역할**

  - AWS 서비스 및 리소스에 액세스할 수 있는 권한을 함수에 부여하는 IAM 역할
  -  Amazon CloudWatch에 로그를 전송하고, AWS X-Ray에 추적 데이터를 업로드하는 권한을 가진 실행 역할을 만들 수 있음

  <br/>

  

# **Lambda 동시성**

---

- **<span style='background-color:#fff5b1'>예약된 동시성</span>**

  - 특정 Lambda 함수에 할당하려는 최대 동시 인스턴스의 수를 예약하는 방법
  - 예약된 동시성을 하나의 함수 전용으로 사용할 경우 **다른 함수는 해당 동시성 사용 불가**
  - 추가 요금 없음

- **<span style='background-color:#fff5b1'>프로비저닝된 동시성</span>** 

  - 사전에 준비된 실행 환경을 통해 콜드 스타트 지연 시간을 줄이고, 서비스 대기 시간을 유지하는 방법
  - 동시성에 따른 자원 사용을 최적화하여 불필요한 **컴퓨팅 비용을 줄일 수 있음**

  - 함수 요청에 **즉시 응답**할 준비가 되도록 해당하는 수의 실행 환경 초기화하여 **콜드 스타트 지연 시간 및 애플리케이션 호출시간/응답시간을 줄일 수 있음**
  - 추가 요금이 발생함

<br/>



