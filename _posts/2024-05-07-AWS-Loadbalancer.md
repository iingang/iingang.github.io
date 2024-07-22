---
title: "[AWS SAA] Load Balancer(로드밸런서) - NLB / ALB / GWLB / CLB "
date: 2024-05-07 09:00:00 +0900
categories: [Cloud,AWS]
tags: [Cloud, AWS, LB, SAA, Load Balancer]
typora-root-url: ./
---





## **Elastic Load Balancer**

---

- 2개 이상의 가용 영역에서 EC2 인스턴스, 컨테이너, IP 주소 등 여러 대상에 걸쳐 **수신되는 트래픽을 자동으로 분산**시키는 관리형 로드밸런서
- 각자 필요에 따라 **NLB, ALB, GWLB, CLB** 에서 가장 적합한 로드밸런서 유형을 선택 가능
- 유지 관리 및 **고가용성**
- 공인 및 사설IP 모두 지원
- EC2, ECS, ACM, CloudWatch, Route 53, WAF, Global Accelerator 등 AWS의 다양한 서비스와 통합하여 확장성과 보안성 강화

<br/>

### **Network Load Balancer(NLB)**

---

- L4 계층(전송 계층)의 로드밸런서로서 **<span style='background-color:#fff5b1'>TCP/UDP</span>** 프로토콜을 지원
- **Elastic IP(=정적 IPv4 주소)를 할당할 수 있고** 공인 및 사설 IP 모두 사용 가능
- 할당한 Elastic IP를 **Static IP(고정 IP)**로 사용하여 **<span style='background-color:#fff5b1'>DNS 이름 및 IP 주소 모두 사용</span>** 가능
- SSL 적용이 인프라 단계에서 불가능하여 어플리케이션에서 별도로 SSL 구성 필요

<br/>

### **Application Load Balancer(ALB)**

---

- L7 계층(어플리케이션 계층)의 로드밸런서로 **<span style='background-color:#fff5b1'>HTTP/HTTPS</span>** 프로토콜을 지원
- IP 주소는 변동되므로 클라이언트에서 ELB의 **<span style='background-color:#fff5b1'>DNS Name을 이용하여 접근</span>**해야 함
- HTTPS 리스너를 설정하려면 로드밸런서에 SSL 서버 인증서를 배포해야 함
- 다양한 **라우팅 알고리즘** 제공
  - **라운드 로빈**
    - 요청을 **순차적**으로 균등하게 분배
  - **최소 미해결 요청 (LOR)**
    - 미해결 요청이 가장 적은 대상에 트래픽을 분산시킴
    - **RequestCount**, **TargetResponseTime** 지표를 기반으로 응답시간을 결정
  - **가중치 기반 랜덤**
    - 가중치를 기반으로 요청을 **무작위**로 균등하게 라우팅
    - 자동 목표 가중치 이상 현상 완화 지원
    - 슬로우 스타트 모드에서는 사용 불가

<br/>

### **Gateway Load Balancer(GWLB)**

---

- 네트워크 트래픽이 어플리케이션으로 이동하기 전 모든 **트래픽을 검사 가능**
- L3 계층(네트워크 계층)의 **<span style='background-color:#fff5b1'>IP 프로토콜</span>** 지원
- **방화벽, 침입 탐지 및 방지** 시스템, **심층 패킷검사**와 같은 가상 어플라이언스를 배포, 확장 및 관리 가능
- 엔드포인트를 사용하여 VPC 경계 간에 트래픽을 안전하게 교환
- AWS Marketplace의 타사 가상 방화벽 어플라이언스와 연계 가능

<br/>

### **Classic Load Balancer(CLB)**

---

- 가장 오래된 형태의 로드밸런서로서 L4와 L7 프로토콜을 모두 지원
- **TCP, SSL, HTTP, HTTPS** 프로토콜을 처리 가능

<br/>



