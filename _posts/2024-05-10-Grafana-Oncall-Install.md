---
title: "[Grafana/OnCall] 그라파나 온콜 설치 방법 (Linux)"
date: 2024-05-10 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Enterprise, OnCall]
typora-root-url: ./
---

## **Grafana Oncall Install Guide**

---

Grafana OnCall은 시스템 어디서나 알람을 수집, 그룹화, 라우팅하는 역할을 한다.

쉽게 말해, OnCall의 Schedules와 Escalation Policies를 통해 프로세스를 자동화하여 적절한 시간에 적절한 대상에게 알림을 제공한다.

<br/>

Grafana 공식 문서에서는 3가지 환경에 대한 설치 방법을 제공한다.

- [production](https://grafana.com/docs/oncall/latest/open-source/#production-environment)
- [developer](https://github.com/grafana/oncall/blob/dev/dev/README.md)
- [hobby](https://grafana.com/docs/oncall/latest/set-up/)

테스트는 hobby 방법으로 진행하겠다. (docker 사용)



<br/>

### **1. Grafana Dashboard Oncall Plugin Install**

---

기존에 사용하고 있는 그라파나 대시보드에서 **[Administration] > [Plugins] > Grafana OnCall** 플러그인을 검색한다.

(만약 그라파나 대시보드도 신규로 같이 설치하고자 하면 2번에서 한번에 설치 가능하다.)

![image-20240711102031407](/../assets/img/posts/2024-05-10-Grafana-Oncall-Install/image-20240711102031407.png)

<br/>

Grafana OnCall 플러그인을 **Install** 한다.

![image-20240711102309884](/../assets/img/posts/2024-05-10-Grafana-Oncall-Install/image-20240711102309884.png)

<br/>

### **2. 도커 컴포즈 파일 다운로드**

---

<https://github.com/grafana/oncall#getting-started>

```bash
curl -fsSL https://raw.githubusercontent.com/grafana/oncall/dev/docker-compose.yml -o docker-compose.yml
```

<br/>

### **3. docker-compose.yml 파일 수정**

---

**`GRAFANA_APU_URL:`** 부분을 연결하고자 하는 그라파나 대시보드 ip:port 혹은 도메인명으로 변경한다.

이 파일에 그라파나 설치 내용도 있기 때문에 신규로 한번에 생성해서 사용해도 된다.

```bash
version: "3.9"

x-environment: &oncall-environment
  DATABASE_TYPE: sqlite3
  BROKER_TYPE: redis
  BASE_URL: $DOMAIN
  SECRET_KEY: $SECRET_KEY
  FEATURE_PROMETHEUS_EXPORTER_ENABLED: ${FEATURE_PROMETHEUS_EXPORTER_ENABLED:-false}
  PROMETHEUS_EXPORTER_SECRET: ${PROMETHEUS_EXPORTER_SECRET:-}
  REDIS_URI: redis://redis:6379/0
  DJANGO_SETTINGS_MODULE: settings.hobby
  CELERY_WORKER_QUEUE: "default,critical,long,slack,telegram,webhook,retry,celery,grafana"
  CELERY_WORKER_CONCURRENCY: "1"
  CELERY_WORKER_MAX_TASKS_PER_CHILD: "100"
  CELERY_WORKER_SHUTDOWN_INTERVAL: "65m"
  CELERY_WORKER_BEAT_ENABLED: "True"
  GRAFANA_API_URL: http://[Grafana IP:PORT]
  ..skip..
```

<br/>

### **4. 컨테이너 생성 및 실행**

---

```bash
docker compose pull && docker compose up -d
```



<br/>

### **5. Grafana Dashboard Oncall URL 설정**

---

설치된 Oncall URL을 입력하고 Connect & Enable 한다.

```bash
OnCall backend URL: http://[Oncall IP]:8080
```

![image-20240711103748457](/../assets/img/posts/2024-05-10-Grafana-Oncall-Install/image-20240711103748457.png)

<br/>

## **References**

---

<https://grafana.com/docs/oncall/latest/set-up/>

<https://github.com/grafana/oncall?tab=readme-ov-file#readme>

<br/>
