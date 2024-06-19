---
title: "[AWS SAA] Container (컨테이너) "
date: 2024-05-06 09:00:00 +0900
categories: [Cloud,AWS]
tags: [Cloud, AWS, Container, SAA]
typora-root-url: ./
---



## **Amazon ECS (Elastic Container Service)**

---

- 클러스터에서 **도커 컨테이너를 손쉽게 관리하기 위한 컨테이너 관리 서비스**
- **EC2 타입**(직접 프로비저닝&유지 필요)과 **Fargate 타입**(관리 필요X)이 있음
- EKS보다 약간 저렴

<br/>

## **AWS Fargate**

---

- 서버리스 컴퓨팅 플랫폼
- 컨테이너를 서버리스 컴퓨팅 플랫폼에서 실행하며, Lambda에 비해 제한 시간이 없음
- 낮은 지연 시간, 스트리밍 환경에 적합한 솔루션

<br/>

## **Amazon ECR (Elastic Container Registry)**

---

- AWS에 도커 이미지를 저장하고 관리하는데 사용
- ECR에 대한 모든 접근은 IAM에서 제어
- 이미지 취약점 스캐닝, 버저닝, 이미지 태그 및 수명주기 확인 지원

<br/>

## **Amazon EKS (Elastic Kubernetes Service)**

---

- AWS에 관리형 kubernetes 클러스터를 실행할 수 있는 서비스
- 컨테이너화한 앱의 자동 배포, 확장, 관리 지원
- `EKS Connector`: 아래의 Kubernetes 클러스터를 AWS에 등록하고 연결할 수 있으며, Amazon EKS 콘솔에서 시각화할 수 있음
  - 온프레미스 Kubernetes 클러스터
  - Amazon EC2에서 실행 중인 자체 관리형 클러스터
  - 다른 클라우드 공급자의 관리형 클러스터

<br/>

## **AWS App Runner**

---

완전 관리형 서비스로 규모에 따라 웹 어플리케이션, API 배포를 돕는 서비스

- 소스 코드나 컨테이너 이미지로 원하는 구성 설정하고 기본 설정 완료하면 빌드 및 배포는 자동으로 진행
- Auto Scailing, 고가용성, 로드 밸런싱, 암호화 기능 지원
- VPC에 액세스 할 수 있어서 데이터베이스, 캐시, 메세지 대기열 서비스에 연결 가능

<br/>
