---
title: "[Confluent] Confluent 6.1 수동 설치"
date: 2023-12-14 14:00:00 +0900
categories: [Messaging System,Confluent]
tags: [Confluent, Message queue, Messaging System]
typora-root-url: ./
---


# **Confluent 6.1 Install 방법**

---

<img src="/../assets/img/posts/2023-12-14-Confluent-Install/CFLT-Logo.png" alt="CFLT-Logo" style="zoom:15%;" />

## User 추가

---

```bash
sudo -i
useradd kafka
```



## SUDO 권한 설정

```bash
echo "kafka ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/kafka
```



## 방화벽 중지 및 비활성화

---

```bash
su - kafka
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```



## SELinux 해제

---

```bash
sudo vi /etc/selinux/config
sudo reboot
```



## Packages 설치

---

```bash
sudo yum install -y gcc make openssl-devel bzip2-devel libfㅣfi-devel xz-devel java-11-openjdk
```



## hosts 파일 설정

---

```bash
sudo vi /etc/hosts
```

```bash
192.168.56.100 [호스트명]
```

> Ansible로 Confluent 설치 시, Python 설치가 필요함



## Confluent Platform 다운로드

---

```bash
curl -O https://packages.confluent.io/archive/6.1/confluent-6.1.0.tar.gz
```





## 압축 해제

---

```bash
tar -zxvf confluent-6.1.0.tar.gz
```



## zookeeper.properies 파일 수정

---

```bash
vi /home/kafka/confluent/confluent-6.1.0/etc/kafka/zookeeper.properies
```

```bash
4lw.commands.whitelist: "*"
initLimit: 5
syncLimit: 2
tickTime: 2000
server.1=localhost:2888:3888
echo 1 > /tmp/zookeeper/myid
```



## Zookeeper myid 저장되는 위치 디렉토리 생성

---

```bash
mkdir -p /tmp/zookeeper
```





## server.properties 파일 수정  (기존 옵션은 주석처리 필요)

---

```bash
vi /home/kafka/confluent/confluent-6.1.0/etc/kafka/server.properties
```

```bash
broker.id=0
listeners=PLAINTEXT://:9092
advertised.listeners=PLAINTEXT://localhost:9092
num.io.threads: 10
num.network.threads: 4
num.replica.fetchers: 12
default.replicaiton.factor: 1
min.insync.replicas: 1
unclean.leader.election.enable: false
metric.reporters: io.confluent.metrics.reporter.ConfluentMetricsReporter
confluent.metrics.reporter.bootstrap.servers: [호스트명]:9092
confluent.metrics.reporter.topic.replicas: 1
offsets.topic.replication.factor: 1
confluent.topic.replication.factor: 1
transaction.state.log.replication.factor: 1
transaction.state.log.min.isr: 1
num.partitions: 1
confluent.license.topic.replication.factor: 1
confluent.metadata.topic.replication.factor: 1
confluent.security.event.logger.exporter.kafka.topic.replicas: 1
confluent.balancer.enable: false
confluent.balancer.topic.replication.factor: 1
delete.topic.enable: true
log.dirs: /home/kafka/confluent/confluent-6.1.0/logs/broker
log.retention.hours: 168
zookeeper.connect=localhost:2181
```



## Zookeeper 실행

---

```bash
sudo /home/kafka/confluent/confluent-6.1.0/bin/zookeeper-server-start /home/kafka/confluent/confluent-6.1.0/etc/kafka/zookeeper.properties
```



## Broker 실행

---

```bash
sudo /home/kafka/confluent/confluent-6.1.0/bin/kafka-server-start /home/kafka/confluent/confluent-6.1.0/etc/kafka/server.properties
```





## ControlCenter 파일 수정

---

```bash
vi /home/kafka/confluent/confluent-6.1.0/etc/confluent-control-center/control-center.properties
```



```bash
confluent.controlcenter.ui.autoupdate.enable: true
confluent.controlcenter.usage.data.collection.enable: true
confluent.controlcenter.command.topic.replication: 1
confluent.monitoring.interceptor.topic.replication: 1
confluent.controlcenter.internal.topics.replication: 1
confluent.monitoring.intercepter.topic.replication: 1
confluent.metrics.topic.replication: 1
confluent.controlcenter.data.dir: /home/kafka/confluent/confluent-6.1.0/logs/controlcenter
confluent.controlcenter.connect.connect-cluster.cluster: http://[호스트명]:8083
```



## ControlCenter 기동

---

```bash
sudo /home/kafka/confluent/confluent-6.1.0/bin/control-center-start /home/kafka/confluent/confluent-6.1.0/etc/confluent-control-center/control-center-production.properties
```



