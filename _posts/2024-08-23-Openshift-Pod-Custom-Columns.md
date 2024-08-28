---
title: "[OpenShift 오픈시프트] oc pod 생성 시간, 상태, IP 주소 및 컨테이너 이미지 출력 방법 (custom-columns, jsonpath 사용)"
date: 2024-08-22 10:30:00 +0900
categories: [Cloud,OpenShift]
tags: [Cloud, OpenShift, RedHat]
typora-root-url: ./
---



# **OpenShift Pod 정보 출력 Customizing 하기**

---

### **Test Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 9.2 (Plow)
>
> RHOCP 4.14.15



<br/>

## **1. Pod 생성시간(creationTimestamp) 출력하기**

---

### **1.1. 하나의 Pod에 대한 Creatation Time 출력**

---

```bash
oc get pod [POD_NAME] -n [NAMESPACE] -o jsonpath='{.metadata.creationTimestamp}'
```

<br/>

**[example]**

```bash
oc get pod dns-operator-676764fcbf-zqrbq -n openshift-dns-operator -o jsonpath='{.metadata.creationTimestamp}'
```

<br/>

**[result]**

```bash
2024-07-19T02:46:34Z
```



<br/>

### **1.2. 여러 Pod에 대한 Creatation Time 출력**

---

전체 생성된 Pod 들에 대한 이름, 네임스페이스, **생성 시간**을 출력할 수 있다.

<br/>

**[example]**

```bash
oc get pods -A -o custom-columns='NAMESPACE:.metadata.namespace,NAME:.metadata.name,CREATION_TIME:.metadata.creationTimestamp'
```

<br/>

**[result]**

```bash
NAMESPACE                                          NAME                                                         CREATION_TIME
openshift-apiserver-operator                       openshift-apiserver-operator-586bcc7998-2dc7b                2024-07-19T02:46:33Z
openshift-apiserver                                apiserver-544c87989-4xbgq                                    2024-07-19T07:02:14Z
openshift-apiserver                                apiserver-544c87989-jstgb                                    2024-07-19T06:59:25Z
openshift-apiserver                                apiserver-544c87989-ks5m7                                    2024-07-19T07:00:47Z
openshift-authentication-operator                  authentication-operator-5c8cbdd485-rshq7                     2024-07-19T02:46:33Z
openshift-authentication                           oauth-openshift-7f87464f54-dmlbc                             2024-08-01T06:59:56Z
openshift-authentication                           oauth-openshift-7f87464f54-q9jmq                             2024-08-01T06:59:00Z
openshift-authentication                           oauth-openshift-7f87464f54-vl2t7                             2024-08-01T06:59:28Z
openshift-cloud-controller-manager-operator        cluster-cloud-controller-manager-operator-7f49dd9989-gppvx   2024-07-19T02:48:34Z
openshift-cloud-credential-operator                cloud-credential-operator-56c5859d5f-qd8mg                   2024-07-19T02:46:33Z
openshift-cluster-machine-approver                 machine-approver-6575c8cbbd-gph5x                            2024-07-19T02:46:33Z
openshift-cluster-node-tuning-operator             cluster-node-tuning-operator-88467b9d9-st57n                 2024-07-19T02:46:33Z
openshift-cluster-node-tuning-operator             tuned-28rfm                                                  2024-07-19T02:50:14Z
..SKIP..
```



<br/>

특정 namespace 에 속한 Pod 들에 대해서도 한 생성 시간을 출력할 수 있다. 

namespace가 openshift-monitoring인 Pod 들의 생성 시간을 살펴보자.

**<br/>**

**[example]**

```bash
oc get pods -n openshift-monitoring -o custom-columns='NAMESPACE:.metadata.namespace,NAME:.metadata.name,CREATION_TIME:.metadata.creationTimestamp'
```

<br/>

**[result]**

```bash
NAMESPACE              NAME                                                     CREATION_TIME
openshift-monitoring   alertmanager-main-0                                      2024-07-19T07:05:28Z
openshift-monitoring   alertmanager-main-1                                      2024-07-19T07:04:56Z
openshift-monitoring   cluster-monitoring-operator-cc696f545-tjpwh              2024-07-19T02:46:34Z
openshift-monitoring   kube-state-metrics-9c56578f7-gjkd5                       2024-07-19T07:04:53Z
openshift-monitoring   monitoring-plugin-5678f6d669-6w6p6                       2024-07-19T05:41:26Z
openshift-monitoring   monitoring-plugin-5678f6d669-crmcw                       2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-ct7nr                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-d27gt                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-jlkv9                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-nw6b7                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-pn49x                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-rtfj2                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-rz6m7                                      2024-07-19T05:41:26Z
openshift-monitoring   node-exporter-zs7tr                                      2024-07-19T05:41:26Z
openshift-monitoring   openshift-state-metrics-f454d74b9-cg9jb                  2024-07-19T07:04:53Z
openshift-monitoring   prometheus-adapter-c6c557db7-64r46                       2024-08-20T08:27:03Z
openshift-monitoring   prometheus-adapter-c6c557db7-8d5m2                       2024-08-20T08:27:03Z
openshift-monitoring   prometheus-k8s-0                                         2024-07-19T07:05:14Z
openshift-monitoring   prometheus-k8s-1                                         2024-07-19T07:04:57Z
openshift-monitoring   prometheus-operator-5786f85b44-vkfpw                     2024-07-19T07:04:43Z
..SKIP..
```

<br/>

## **2. Pod 상태(Status) 출력하기**

---

현재 생성된 전체 Pod들에 대한 **상태**를 나타낼 수 있다.

<br/>

**[example]**

```bash
oc get pods -A -o custom-columns='NAMESPACE:.metadata.namespace,NAME:.metadata.name,STATUS:.status.phase'
```

<br/>

**[result]**

```bash
NAMESPACE                                          NAME                                                         STATUS
openshift-apiserver-operator                       openshift-apiserver-operator-586bcc7998-2dc7b                Running
openshift-apiserver                                apiserver-544c87989-4xbgq                                    Running
openshift-apiserver                                apiserver-544c87989-jstgb                                    Running
openshift-apiserver                                apiserver-544c87989-ks5m7                                    Running
..SKIP..
openshift-etcd                                     etcd-guard-master01.openshift.ig.local                       Running
openshift-etcd                                     etcd-guard-master02.openshift.ig.local                       Running
openshift-etcd                                     etcd-guard-master03.openshift.ig.local                       Running
openshift-etcd                                     etcd-master01.openshift.ig.local                             Running
openshift-etcd                                     etcd-master02.openshift.ig.local                             Running
openshift-etcd                                     etcd-master03.openshift.ig.local                             Running
openshift-etcd                                     installer-4-master02.openshift.ig.local                      Succeeded
openshift-etcd                                     installer-6-master02.openshift.ig.local                      Succeeded
openshift-etcd                                     installer-6-master03.openshift.ig.local                      Succeeded
```

<br/>

## **3. Pod IP 주소(IP Address) 출력하기**

---

전체 Pod들에 대한 **IP 주소**와 속한 **Node 이름**을 출력할 수 있다.

<br/>

**[example]**

```bash
oc get pods -A -o custom-columns="NAME:.metadata.name,IP:.status.podIP,NODE:.spec.nodeName"
```



<br/>

**[result]**

```bash
NAME                                                         IP             NODE
openshift-apiserver-operator-586bcc7998-2dc7b                10.12.0.X     master03.openshift.ig.local
apiserver-544c87989-4xbgq                                    10.13.0.XXX   master02.openshift.ig.local
apiserver-544c87989-jstgb                                    10.12.0.XX    master03.openshift.ig.local
apiserver-544c87989-ks5m7                                    10.12.0.XX    master01.openshift.ig.local
authentication-operator-5c8cbdd485-rshq7                     10.12.0.X     master03.openshift.ig.local
```

<br/>

## **4. Pod의 Container Image 및 Number of retries 출력하기**

---

전체 Pod들에 대한 **컨테이너 이미지명**과 **재시도 횟수**를 출력할 수 있다.

<br/>

**[example]**

```bash
oc get pods -A -o custom-columns="NAME:.metadata.name,IMAGE:.spec.containers[*].image,RESTARTS:.status.containerStatuses[*].restartCount"
```

<br/>

**[result]**

```bash
NAME                                                         IMAGE
installer-6-master03.openshift.ig.local                      quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:XXXXX
installer-8-master02.openshift.ig.local                      quay.io/openshift-release-dev/ocp-v4.0-art-dev@sha256:XXXXX
```





<br/>

## **References**

---

<https://docs.openshift.com/container-platform/4.14/cli_reference/openshift_cli/developer-cli-commands.html>

<br/>
