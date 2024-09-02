---
title: "[OpenShift 오픈시프트] Web Console에서 프로젝트 생성 시, Pipeline 서비스 계정(SA) 생성 안 되는 현상 (feat.Openshift Pipelines Operator)"
date: 2024-08-25 10:00:00 +0900
categories: [Cloud,OpenShift]
tags: [Cloud, OpenShift, RedHat]
typora-root-url: ./
---



# **OpenShift Tekton Config 오류 (Cluster Role 관련)**

---

### **Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 8.6
>
> RHOCP 4.14.13
>
> OpenShift Pipelines Operator 1.14.0



<br/>

## **[Symtomps]**

---

**Openshift Pipelines Operator**를 설치한 상태에서, OCP 웹 콘솔에서 **`Project`**를 생성하면 원래 자동으로 **`pipeline`**이라는 이름의 **Service Account(서비스 계정)**이 생성된다.

그런데 프로젝트를 만들어도 서비스 계정도 생성되지 않고, Trigger 메뉴에서 이벤트 리스너를 생성하고 pod가 올라오는 도중에 CrachLoopBackOff 에러가 나는 상황이 발생했다.

<br/>

### **1. Tekton Config 상태 확인**

---

**`oc get tektonconfig`**

```bash
NAME     VERSION   READY   REASON
config   1.14.0    False   PreReconciliation failed with message: rolebindings.rbac.authorization.k8s.io "pipelines-scc-rolebinding" not found
```

<br/>

 Tekton Config의 상태가 False 이므로 Openshift에서 **`pipelines-scc-rolebinding`** 이름을 가진 rolebinding이 없어 문제가 발생한 것으로 보인다.

<br/>

### **2. Pipelines Rolebinding이 삭제된 user namespace가 있는지 확인**

---

아래와 같이 나와야 정상인데, 에러가 발생했을 때는 역할 바인딩이 존재하지 않았다.

<br/>

**`oc get rolebinding pipelines-scc-rolebinding -n <namespace>`**

```bash
NAME                        ROLE                                    AGE
pipelines-scc-rolebinding   ClusterRole/pipelines-scc-clusterrole   22h
```



<br/>

### **3. reconcile-version 값이 포함되어 있는지 확인**

---

2번을 실행 했을 때, 아무 내용이 나오지 않았다면 아래 명령어를 실행했을 때 **reconcile-version** 관련된 값이 포함되어 있는지 확인하자.

<br/>

**`oc get namespace <namespace> -o yaml`**

```bash
openshift-pipelines.tekton.dev/namespace-reconcile-version: <version>
```

<br/>

## **[Cause]**

---

이 이슈의 근본적인 원인은 어떤 네임스페이스에서 파이프라인-scc-rolebinding 역할 바인딩이 누락되거나 삭제된 경우 OpenShift 파이프라인 운영자 컨트롤러가 새 네임스페이스에서 기본 리소스를 올바르게 생성하지 못하는 버그 때문이다.

이로 인해, <span style="background-color:#fff5b1">pipelines-scc-rolebinding이 삭제된 namespace가 있어, tektonconfig가 False 상태가 되면서 Reconcilation Loop 에 갇혀, 신규 생성된 namespace에 대해 sa 및 rolebinding을 생성하지 못한 것으로 보여진다.</span>

이 버그는 Pipelines 1.14.4에 Fix 되었으며, 버전 업그레이드 않고 해결하는 방법은 아래와 같다.



<br/>



## **[Solutions]**

---

### **1. 레이블(label)이 있으면 네임스페이스에서 제거**

---

**`oc label namespace <namespace> openshift-pipelines.tekton.dev/namespace-reconcile-version-`**



<br/>

### **2. Tekton Config 상태 다시 확인**

---

tektonconfig의 Ready 상태가 True로 바뀌었으며, 프로젝트를 생성하면 정상적으로 서비스 계정이 생기고, 이벤트 리스너 pod도 잘 올라오는 것을 볼 수있다.

<br/>

**`oc label namespace <namespace> openshift-pipelines.tekton.dev/namespace-reconcile-version-`**

```bash
NAME     VERSION   READY   REASON
config   1.14.0    True
```

<br/>





<br/>

## **References**

---

<https://access.redhat.com/solutions/7062253>

<https://docs.openshift.com/pipelines/1.14/about/op-release-notes.html#release-notes-1-14-4_op-release-notes>

<https://issues.redhat.com/browse/SRVKP-4492>

<br/>
