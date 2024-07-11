---
title: "[Confluent] 컨플루언트 Cluster Link 생성 방법 (Mirror Topic 사용)"
date: 2023-12-27 09:00:00 +0900
categories: [Messaging System,Confluent]
tags: [Confluent, Message queue, Messaging System]
typora-root-url: ./
---


# **Confluent 클러스터 링크 생성하기**

---

## **Cluster Linking 이란?**

---

Kafka 클러스터 간의 직접적인 연결을 통해 한 클러스터에서 다른 클러스터로 토픽을 미러링할 수 있는 기능이다.

**`Cluster Linking`**의 주요 특징은 다음과 같다.

- **직접 연결**: 클러스터를 직접 연결하여 토픽을 미러하기 때문에 메시지 복사에 필요한 Connect를 실행할 필요가 없다.

- **안전성과 성능**: Cluster Linking은 안전하고 성능이 뛰어나며 네트워크 대기시간을 견딜 수 있다.

- **Confluent Server 및 Confluent Cloud 내장**: 이 기능은 Confluent Server와 Confluent Cloud에 내장되어 있어 별도의 설치나 설정 없이 사용할 수 있다.

- **글로벌 오프셋 일관성**: Cluster Linking은 전역적으로 일관된 offset을 사용하여 동일한 "mirror 토픽"을 생성하므로 여러 클러스터 간의 데이터 일관성을 유지할 수 있다.

<br/>

### **Test Environment**

----

{: .prompt-info }

> cp-all-in-one 7.5
>
> Connector 2개 사용 (FileStream SinkConnector, Datagen Source connector)

{: .prompt-tip }

> **Connector를 사용하는 이유**
>
> 커넥터를 설정하여 offset을 계속 증가시키는 역할
>
> - datagen source connector 로 users topic 생성
>
> - filestream sink connector 로 /tmp/users.txt 파일로 가져옴
>
> 위 설정으로 connector consumer group offset은 계속 증가중

<br/>

### **1. Broker 설정 추가**

---

**`vi ${CONFLUENT_HOME}/etc/kafka/server.properties`** **(Source: ig-test1)**

```bash
confluent.http.server.listeners=http://0.0.0.0:8090
```

<br/>

**`vi ${CONFLUENT_HOME}/etc/kafka/server.properties`** **(Destination: ig-test2)**

```bash
confluent.http.server.listeners=http://0.0.0.0:8090
confluent.cluster.link.metadata.topic.replication.factor=1
confluent.cluster.link.enable=true
```



<br/>

### **2. Cluster Link 생성 (Destination)**

---

```bash
[kafka@ig-test2 ~]$ kafka-cluster-links --bootstrap-server ig-test2:9092 \
>       --create --link test-link --config bootstrap.servers=ig-test1:9092
Cluster link 'test-link' creation successfully completed.
```



<br/>

### **3. Mirror Topic 생성 (Destination)**

---

**`test-topic`**은 **ig-test1**에 있다.

Consumer Group과 Offset까지 복제해주기 위해 Config 설정을 추가하여 Mirror Topic을 생성하고자 한다.

2개의 config 파일을 생성해준다.

**`vi config.txt`**

```bash
consumer.offset.sync.enable=true
bootstrap.servers=ig-test1:9092
```

<br/>

**`vi config.json`**

```bash
{"groupFilters": [
  {
    "name": "connect-FileStreamSinkConnectorConnector_1",
    "patternType": "LITERAL",
    "filterType": "INCLUDE"
  }
]}
```

<br/>

옵션을 추가하여 Mirror 토픽을 생성한다.

```bash
[kafka@ig-test2 kafka]$ kafka-cluster-links --bootstrap-server ig-test2:9092 --create --link test-link --config-file config.txt --consumer-group-filters-json-file config.json
Cluster link 'test-link' creation successfully completed.
```

<br/>



test-topic에 대한 Mirror Topic이 생성된 것을 볼 수 있다.

![image](/../assets/img/posts/2023-12-27-Confluent-Cluster-Link/image.png)<br/>

### **4. Mirror Topic으로 데이터 전송**

---

```bash
[kafka@ig-test2 ~]$ kafka-console-producer --bootstrap-server ig-test2:9092 --topic test-topic
>2
>3
>4
```

<br/>

위와 같이 ig-test2의 mirror topic에 데이터를 전송하면 **`read-only`** 이기 때문에 오류가 발생한다.

```bash
[2023-12-05 07:39:17,571] ERROR Error when sending message to topic users with key: null, value: 1 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)
org.apache.kafka.common.errors.InvalidRequestException: Cannot append records to read-only mirror topic 'users'
```

<br/>



### **5. Mirror Topic 복제 확인**

---

ig-test2에서 consumer-group, offset까지 다 잘 복제된 것을 확인할 수 있다.

![image](/../assets/img/posts/2023-12-27-Confluent-Cluster-Link/image-1720666444850-2.png)

<br/>

### **6. Mirror Topic을 일반 Topic으로 변환**

---

4번에서 발생한 오류를 해결하기 위해 Mirror Topic을 Normal Topic으로 승격시켜줘야 한다.

그 방법으로 **`failover`**와 **`promote`** 옵션이 있다. 둘 다 일반 토픽으로 변환시켜 주지만 약간의 차이점이 있다.

- **`promote`**
  - **사용 목적**: 마이그레이션 상황에서 정확한 전환
  - **검사 항목**: 미러링 지연(mirroring lag), 설정 동기화 지연(config sync lag), 소비자 오프셋 지연(consumer offset lag)이 없는지 확인
  - **보장**: 미러 토픽을 전체 토픽으로 변환하기 전에, 소스 토픽과 미러 토픽 간의 일치성 확인 (즉, 미러 토픽이 소스 토픽과 정확히 동일하다는 보장)
- **`failover`**
  - **사용 목적**: 재해 복구 상황에서 긴급한 전환
  - **검사 항목**: 미러링 지연이나 소스 클러스터의 도달 가능성 여부와 상관없이 명령 성공
  - **보장**: 미러링 지연이 있거나 소스 클러스터가 도달할 수 없는 상황에서도 실행되므로, 소스 토픽과 미러 토픽이 일치하지 않을 수 있음



{: .prompt-warning }

> `failover` 및 `promote`명령은 되돌릴 수 없다. 이 토픽을 다시 미러 토픽으로 변경할 방법은 없으므로, 이 클러스터에서 승격하거나 장애 조치한 토픽과 같은 이름의 미러 토픽이 필요한 경우 변환된 토픽을 삭제하고 같은 이름의 새 미러 토픽을 만들어야 한다.

<br/>

여기서는 **`faiilover`** 옵션을 적용하여 미러 토픽을 일반 토픽으로 변경해주었다.

```bash
[kafka@ig-test2 kafka]$ kafka-mirrors --bootstrap-server ig-test2:9092 --link test-link --failover
Request for stopping topic users's mirror was successfully scheduled. Please use the describe command with the --pending-stopped-only option to monitor progress.
```

이제 ig-test2의 test-topic은 독립된 토픽으로, consumer group과 offset도 더 이상 동기화 되지 않는 것을 확인할 수 있다.

<br/>

## **References**

---

<https://docs.confluent.io/platform/current/multi-dc-deployments/cluster-linking/topic-data-sharing.html#tutorial-use-cluster-linking-to-share-data-across-topics>

<https://docs.confluent.io/platform/current/multi-dc-deployments/cluster-linking/mirror-topics-cp.html>

<https://docs.confluent.io/platform/7.5/multi-dc-deployments/cluster-linking/hybrid-cp.html#configure-ports-data-directories-authentication-and-cluster-linking-specifics>

<br/>
