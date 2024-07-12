---
title: "[Confluent] Confluent Connector 설치 및 생성"
date: 2023-12-16 09:00:00 +0900
categories: [Messaging System,Confluent]
tags: [Confluent, Message queue, Messaging System]
typora-root-url: ./
---


# **Confluent 커넥터 설치**

---



## **Datagen Connector 및 FileStream Connector 설치 가이드**

---

### **설치 방법**

---

Connector를 설치하는 방법은 다양하다.

1. hosts.yml 파일에 넣어 Ansible로 설치
2. confluent-hub를 통해 설치
3. docker image에 설치
4. 수동 설치

<br/>

### **Datagen Source Connector 생성**

---

Datagen Source Connector를 설치한 후, 아래와 같은 설정 파일을 사용하여 Kafka Connect에 연결할 수 있다.

```bash
confluent-hub install confluentinc/kafka-connect-datagen:latest
```

```json
{
  "name": "datagen-users",
  "config": {
    "connector.class": "io.confluent.kafka.connect.datagen.DatagenConnector",
    "kafka.topic": "users",
    "quickstart": "users",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "value.converter.schemas.enable": "false",
    "max.interval": 1000,
    "iterations": 10000000,
    "tasks.max": "1"
  }
}
```

위 설정은 `datagen-users`라는 이름으로 Datagen Source Connector를 설정하며, `users` 토픽에 데이터를 생성한다.

<br/>

### **FileStream Sink Connector 생성**

---

FileStream Connector 은 기본 설치가 되므로, 설치된 커넥터에 경로 설정을 해줘야 한다.

FileStream Connector를 사용하여 데이터를 파일로 저장할 수 있다. 

버전 6.2.1부터 FileStream Sink 및 Source 커넥터 아티팩트가 Kafka Connect 외부로 이동되어,  FileStream 커넥터를 실행하려면 `plugin.path`에 표시된 대로 구성 속성에 새 경로를 추가해야 한다.

<br/>

**FileStream Connector 관련 plugin path 수정**

```bash
vi /app/confluent/etc/kafka/connect-distributed.properties
```

```bash
plugin.path=/app/confluent/confluent-7.5.0/share/filestream-connectors
```

<br/>

**FileStream Connector 설정**

```bash
name=local-file-sink
connector.class=FileStreamSink
tasks.max=1
file=/tmp/test.sink.txt
topics=connect-test
```

위 설정은 `connect-test` 토픽의 데이터를 `/tmp/test.sink.txt` 파일에 저장하도록 한다.



```json
{
  "name": "FileStreamSinkConnectorConnector_1",
  "config": {
    "value.converter.schemas.enable": "false",
    "name": "FileStreamSinkConnectorConnector_1",
    "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
    "tasks.max": "1",
    "key.converter": "org.apache.kafka.connect.storage.StringConverter",
    "value.converter": "org.apache.kafka.connect.json.JsonConverter",
    "topics": "users",
    "file": "/tmp/users.txt"
  }
}
```



<br/>

### **정상 동작 확인**

---

아래와 같이 설정하면, `/tmp/users.txt` 파일에 Datagen Source Connector로 생성된 `users` 데이터가 잘 쌓이는 것을 확인할 수 있다.

<br/>

![image](/../assets/img/posts/2024-01-03-Confluent-Connector/image-1718595679208-2.png)

![image](/../assets/img/posts/2024-01-03-Confluent-Connector/image.png)



<br/>

## References

---

[https://docs.confluent.io/kafka-connectors/datagen/current/overview.html](https://docs.confluent.io/kafka-connectors/datagen/current/overview.html)

[https://docs.confluent.io/platform/current/connect/filestream_connector.html](https://docs.confluent.io/platform/current/connect/filestream_connector.html)

[https://github.com/confluentinc/kafka-connect-datagen/tree/master/config](https://github.com/confluentinc/kafka-connect-datagen/tree/master/config)

<br/>
