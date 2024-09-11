---
title: "[OpenShift 오픈시프트] Openshift Logging / 오픈시프트 로그 이해하기"
date: 2024-09-02 10:00:00 +0900
categories: [Cloud,OpenShift]
tags: [Cloud, OpenShift, RedHat]
typora-root-url: ./
---



# **Openshift Logging**

---

## **Openshift Logging 개요**

---

클러스터 관리자는 OpenShift Container Platform 클러스터에 로깅을 배포하고, 이를 사용하여 노드 시스템 **감사 로그(Audit Log)**, **애플리케이션 컨테이너 로그(Application Log)** 및 **인프라 로그(Infrastructure Log)**를 수집하고 집계할 수 있습니다.

<br/>

- **`application`** - 인프라 컨테이너 애플리케이션을 제외하고 클러스터에서 실행 중인 사용자 애플리케이션에 의해 생성된 컨테이너 로그입니다.

- **`infrastructure`** - 저널 로그와 같이 클러스터 및 OpenShift Container Platform 노드에서 실행되는 인프라 구성 요소에서 생성된 로그입니다. 인프라 구성 요소는 `openshift*`, `kube*` 또는 `default` 프로젝트에서 실행되는 Pod입니다.

- **`audit`** - **/var/log/audit/audit.log** 파일에 저장되는 노드 감사 시스템(auditd)에서 생성된 로그와 Kubernetes apiserver 및 OpenShift apiserver에서 생성되는 감사 로그입니다.



<br/>

## **Openshift Logging Operator**

---

OperatorHub에서 Red Hat Openshift Logging Operator를 설치합니다. (Disconnected 환경에서는 Mirror Registry를 구성하여 필요한 이미지를 로컬 레지스트리로 당겨와서 설치 가능)

![image-20240911232551563](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240911232551563.png)

<br/>

-- 추가로 작성 예정 --

<br/>

## **References**

---

<https://docs.openshift.com/container-platform/4.14/observability/logging/cluster-logging.html>

<https://docs.openshift.com/container-platform/4.14/observability/logging/cluster-logging-deploying.html#cluster-logging-deploying>

<br/>

<br/>
