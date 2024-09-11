---
title: "[OpenShift 오픈시프트] Openshift RBAC(Role-based access control) / 권한 설정"
date: 2024-09-02 10:00:00 +0900
categories: [Cloud,OpenShift]
tags: [Cloud, OpenShift, RedHat]
typora-root-url: ./
---



# **OpenShift RBAC: Applying Permissions**

---

## **Openshift RBAC 개요**

---



**OpenShift RBAC(Role-Based Access Control)**은 OpenShift 클러스터에서 사용자와 그룹에게 적절한 권한을 할당해 리소스에 대한 접근을 제어하는 보안 시스템입니다. Kubernetes의 RBAC 모델을 기반으로 하며, 역할(Role)과 바인딩(Binding)을 사용해 클러스터 내에서 누구에게 어떤 리소스에 대한 접근 권한을 부여할지 결정합니다.

<br/>

**역할(Role)**: 역할은 사용자가 특정 리소스에 대해 수행할 수 있는 작업(권한)을 지정합니다. 예를 들어, Role을 통해 사용자가 특정 네임스페이스의 `pod` 리소스를 조회할 수 있도록 할 수 있습니다.

**바인딩(Binding)**: 바인딩은 특정 역할을 사용자 또는 그룹에 연결하여, 해당 역할에서 정의한 권한을 그들에게 부여합니다.

<br/>

OpenShift에서 **`ClusterRole`**, **`Role`**, **`ClusterRoleBinding`**, **`RoleBinding`**은 **RBAC(Role-Based Access Control)**에서 사용하는 중요한 객체들입니다. 

각 객체는 사용자, 그룹, 또는 서비스 계정에 특정 리소스에 대한 액세스 권한을 부여하는 역할을 합니다. 아래는 각 객체에 대한 설명입니다.

<br/>

**Cluster RBAC (클러스터 RBAC)**: 클러스터 전체에 적용되는 역할과 바인딩을 관리합니다.

>  *ClusterRole*은 클러스터 전체에 존재하며 ***ClusterRoleBinding***은 Cluster Role만 참조할 수 있습니다.

- **`ClusterRole(클러스터 역할)`**: 클러스터 전체에 걸쳐 리소스에 대한 권한을 부여하며, 특정 project(=namespace) 또는 클러스터 전역에서 권한을 정의할 수 있습니다.

- **`ClusterRoleBinding(클러스터 역할 바인딩)`**:  클러스터 전역에서 사용자나 그룹에게 ClusterRole을 할당합니다.



<br/>

**Local RBAC (로컬 RBAC)**: 특정 프로젝트에만 적용되는 역할과 바인딩을 관리합니다.

> *Role은* 단일 프로젝트에만 존재하지만 ***RoleBinding***은 ClusterRole과 Role을 **모두 참조**할 수 있습니다.

- **`Role(역할)`**: 특정 리소스에 대해 수행할 수 있는 작업(예: 읽기, 쓰기, 수정)을 정의하며, project(=namespace) 내에서만 적용됩니다.
- **`RoleBinding(역할 바인딩)`**:  project(=namespace) 내에서 특정 사용자나 그룹에게 Role을 할당합니다.



<br/>

다음은 OpenShift에서 사용되는 다양한 역할(Role)들의 권한을 설명한 내용입니다. 각 역할에 따라 사용자들이 특정 프로젝트나 클러스터 내에서 수행할 수 있는 작업이 다릅니다.

| Cluster Role     | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| admin            | 프로젝트 관리자. 로컬 바인딩(local binding)에서  사용될 경우, 해당 프로젝트 내의 모든 리소스를 볼 수 있고, 할당량(quota)을 제외한 모든 리소스를 수정할 수 있는 권한이 있습니다. |
| basic-user       | 프로젝트와 사용자에 대한 기본 정보를 조회할 수 있는  사용자입니다. |
| cluster-admin    | 슈퍼 유저로, 모든 프로젝트에서 모든 작업을 수행할 수  있습니다. 로컬 바인딩에서 사용자에게 할당될 경우, 프로젝트 내의 할당량을 포함한 모든 리소스에 대한 완전한 제어 권한을 가집니다. |
| cluster-status   | 클러스터의 기본 상태 정보를 조회할 수 있는 사용자입니다.     |
| cluster-reader   | 대부분의 객체를 조회할 수 있지만 수정할 수는 없는  사용자입니다. |
| edit             | 프로젝트 내에서 대부분의 객체를 수정할 수 있는 사용자이지만,  역할(Role)이나 바인딩(Binding)을 조회하거나 수정할 수는 없습니다. |
| self-provisioner | 자신만의 프로젝트를 생성할 수 있는 사용자입니다.             |
| view             | 프로젝트 내에서 대부분의 객체를 볼 수 있지만 수정할 수는  없으며, 역할(Role)이나 바인딩(Binding)을 조회하거나 수정할 수 없습니다. |

<br/>

## **User 및 Group 생성 (권한 부여)**

---

-- 이 부분은 추가로 작성 예정 --

<br/>

## **References**

---

<https://docs.redhat.com/ko/documentation/openshift_container_platform/4.11/html/post-installation_configuration/post-install-using-rbac-to-define-and-apply-permissions>

<https://docs.openshift.com/container-platform/4.14/authentication/using-rbac.html>

<br/>

<br/>
