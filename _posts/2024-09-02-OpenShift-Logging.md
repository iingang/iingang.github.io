---
title: "[OpenShift 오픈시프트] Openshift Logging / Loki Operator / Web Console Log Monitoring"
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

- **`application`** - 인프라 컨테이너 애플리케이션을 제외하고 클러스터에서 실행 중인 사용자 애플리케이션에 의해 생성된 컨테이너 로그입니다. 이는 개발자나 운영자가 배포한 애플리케이션에서 발생하는 로그를 포함하며, 각 애플리케이션의 상태, 오류, 성능과 관련된 정보를 제공합니다.



- **`infrastructure`** - 사용자 namespace의 로그를 제외하고, 플랫폼 레벨에서 생성되는 로그를 의미합니다. 여기에는 OpenShift 컨테이너 플랫폼의 각종 컴포넌트들이 위치한 네임스페이스에서 발생하는 컨테이너 로그와 노드의 **journald** 메시지 로그가 포함됩니다. 간단히 말해, OpenShift Container Platform의 다양한 시스템 컴포넌트들이 위치한 네임스페이스에서 생성되는 로그들을 뜻합니다.



- **`audit`** - **/var/log/audit/audit.log** 파일에 저장되는 노드 감사 시스템(auditd)에서 생성된 로그와 Kubernetes apiserver 및 OpenShift apiserver에서 생성되는 감사 로그입니다. 간단히 말해, 시스템에 호출된 Request 들에 대한 logging을 하며 시스템 변경에 대한 감사를 위해 수집되는 로그입니다.



<br/>

## **Openshift Logging Operator**

---

OpenShift Logging Operator는 기본적으로 OpenShift 클러스터의 **통합 로그 관리**를 제공합니다. 

클러스터 내에서 발생하는 **모든 로그(애플리케이션 로그, 인프라 로그, 감사 로그)**를 수집하고 관리할 수 있도록 지원합니다. 

<br/>

OperatorHub에서 Red Hat Openshift Logging Operator를 설치할 수 있습니다. 

> Disconnected 환경에서는 Mirror Registry를 구성하여 필요한 이미지를 로컬 레지스트리로 당겨와서 설치

<br/>

![image-20240911232551563](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240911232551563.png)

<br/>

![image-20240922203638412](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922203638412.png)

<br/>

## **Loki Operator**

---

Loki Stack은 주로 **애플리케이션 로그**를 효율적으로 수집하고 분석하는 데 중점을 둡니다. 

특히, 기존의 로그 스토리지 솔루션인 Elasticsearch와 달리, **라벨링된 로그**를 중심으로 데이터를 저장하며, **메트릭 기반 모니터링** 도구인 Prometheus와 함께 사용하는 경우가 많습니다.

<br/>

OperatorHub에서 Loki Operator를 설치할 수 있습니다. 

> Disconnected 환경에서는 Mirror Registry를 구성하여 필요한 이미지를 로컬 레지스트리로 당겨와서 설치

<br/>

![image-20240918223329136](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240918223329136.png)

<br/>

![image-20240922203451341](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922203451341.png)

<br/>

Loki는 로그 데이터를 저장하고 쿼리하기 위해 스토리지를 필요로 합니다. 

또한 Loki는 다양한 스토리지 옵션을 지원하며, 선택한 스토리지 옵션에 따라 설정 방법이 달라집니다. 

주요 스토리지 유형은 이 [문서](https://loki-operator.dev/docs/object_storage.md/)를 참고해주세요.

<br/>

## **OCP 웹 콘솔 로그 모니터링**

---

OCP 웹 콘솔에서 로그를 모니터링하기 위해서는 Openshift Logging 오퍼레이터에서 **ClusterLogging / ClusterLogForwarder** 구성을 해야 합니다. 

{: .prompt-tip}

> **ClusterLogging**: OpenShift 클러스터에서 로그 수집, 저장, 및 시각화와 관련된 전체 로깅 스택을 정의하고 관리하는 객체
>
> **ClusterLogForwarder**: 로그를 수집한 후 이를 **전달할 목적지(backend)를 정의**하는 리소스



<br/>

**`ClusterLogging`**은 다음과 같이 collection, logStore, visualization 타입을 지정하여 설정할 수 있습니다.

![image-20240922202049744](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922202049744.png)

<br/>

![image-20240922202130675](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922202130675.png)

<br/>

![image-20240922202151510](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922202151510.png)

<br/>

여기서는 **`ClusterLogging`**을 다음과 같이 생성하였습니다.

```bash
apiVersion: logging.openshift.io/v1
kind: ClusterLogging
metadata:
  name: instance
  namespace: openshift-logging
spec:
  collection:
    type: vector
  logStore:
    lokistack:
      name: lokistack-test
    retentionPolicy:
      application:
        maxAge: 1h
      audit:
        maxAge: 1h
      infra:
        maxAge: 1h
    type: lokistack
  managementState: Managed
  visualization:
    type: ocp-console	
```

{: .prompt-info}

> - collection: **vector** --> 로그 수집 및 전송 역할을 하는 방식
> - logStore type: **lokistack** --> Loki 기반의 저장 솔루션
> - visualization type: **ocp-console** --> ocp 웹 콘솔에서 로그 시각화
>

<br/>

**`ClusterLogForwarder`**은 다음과 같이 생성하였습니다.

이 때, 위 2개의 설정 모두  instance라는 같은 이름으로 해줘야 오류없이 배포가 가능했습니다. ([참고 문서](https://issues.redhat.com/browse/LOG-5062))

```bash
apiVersion: logging.openshift.io/v1
kind: ClusterLogForwarder
metadata:
  name: instance
  namespace: openshift-logging
spec:
  pipelines:
    - inputRefs:
        - application
        - audit
        - infrastructure
      outputRefs:
        - default
```

<br/>

Loki Operator에서 **`LokiStack`**도 위에서 클러스터 로깅에 설정한 **lokistack-test** 이름으로 설정해주면 되며, 사용하는 스토리지에 맞게 구성해주시면 됩니다.

정상적인 로그 모니터링을 하기 위해 **`local storage`**와  **`secret`** 설정도 필요하지만 이 내용은 생략하겠습니다.



<br/>



## **결과 화면**

---

로깅 설정이 잘 되면 다음과 같이 상태가 **Ready**로 나오게 됩니다.

![image-20240922200227364](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922200227364.png)

<br/>

OCP 웹 콘솔에서 **모니터링(Observe) > 로그(Logs)** 메뉴가 생성된 것을 볼 수 있습니다.



![image-20240922200311271](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922200311271.png)

<br/>

![image-20240922201319307](/../assets/img/posts/2024-09-02-OpenShift-Logging/image-20240922201319307.png)

<br/>

## **References**

---

<https://docs.openshift.com/container-platform/4.14/observability/logging/cluster-logging.html>

<https://docs.openshift.com/container-platform/4.14/observability/logging/cluster-logging-deploying.html#cluster-logging-deploying>

<https://docs.openshift.com/container-platform/4.14/observability/logging/log_storage/installing-log-storage.html>

<https://docs.openshift.com/container-platform/4.14/observability/logging/log_storage/cluster-logging-loki.html>

<br/>

<br/>
