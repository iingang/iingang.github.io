---
title: "[Grafana 그라파나] Grafana Install Guide (Linux)"
date: 2024-01-07 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Enterprise]
typora-root-url: ./
---

## **OCI 환경 그라파나 설치 방법**

---

> OS: Oracle Linux 8.8
>
> Version: Grafana Enterprise 8.2.3

<br/>

### **OCI에 Test 인스턴스 생성**

---

![image-20240618095948890](/../assets/img/posts/2024-01-07-Grafana-Install/image-20240618095948890.png)

<br/>

### **grafana User 및 Password 생성**

---

```bash
sudo useradd grafana
sudo passwd grafana
[password]
```

<br/>

### **sudo 권한 부여**

---

```bash
echo "grafana ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/grafana
```

<br/>

### **Grafana 설치 파일 다운로드**

---

```bash
wget https://dl.grafana.com/enterprise/release/grafana-enterprise-8.2.3.linux-amd64.tar.gz
```

> 아래와 같이 yum으로 설치하는 방법도 있다.
>
> ```bash
> yum install -y https://dl.grafana.com/enterprise/release/grafana-enterprise-10.4.1-1.x86_64.rpm
> systemctl start grafana.service
> ```

<br/>

### **설치 파일 압축 해제**

---

```bash
tar -xvf grafana-enterprise-8.2.2.linux-amd64.tar.gz
```

<br/>

### **SELinux 해제**

---

```
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

```
setenforce 0
```

<br/>

### **방화벽 중지 및 해제**

---

```bash
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```



<br/>

### **네트워크 재시작**

---

```bash
sudo systemctl restart NetworkManager.service
```

<br/>

### **Systemd Service 등록**

---

```bash
sudo vi /etc/systemd/system/grafana-server.service
```

```bash
[Unit]
Description=Grafana Server
Documentation= https://grafana.com/docs/grafana/latest/
Wants=network-online.target
After=network-online.target

[Service]
User=grafana
Group=grafana
Type=simple
Restart=on-failure
WorkingDirectory=/home/grafana/8.2.3
ExecStart=/home/grafana/8.2.3/bin/grafana-server

[Install]
WantedBy=multi-user.target
```

<br/>

### **서비스 설정 반영**

---

```bash
sudo systemctl daemon-reload
```

<br/>

### **그라파나 서비스 시작**

---

```bash
sudo systemctl start grafana-server
```

<br/>

#### **오류 발생**

---

SELinux is preventing /usr/lib/systemd/systemd from execute access on the file grafana-server.

> SELinux는 ExecStart에서 사용할 수 있는 바이너리를 system_u:object_r:bin_t:s0 속성이 설정된 경로로 제한합니다. 일반적으로 이는 /usr/bin /usr/sbin /usr/libexec /usr/local/bin 디렉토리입니다. systemd가 원하는 위치에서 바이너리를 사용할 수 있도록 스크립트를 이 디렉터리 중 하나로 이동하거나 selinux 정책을 변경해야 합니다.

<br/>

#### **해결 방법**

---

그라파나 설치 디렉토리에 SELinux 정책 변경 적용
```
chcon -R -t bin_t /home/grafana
semanage fcontext -a -t bin_t "/home/grafana(/.*)?"
restorecon -r -v /home/grafana
```

<br/>

### **서비스 시작 후 상태 확인**

---

![image-20240618101706124](/../assets/img/posts/2024-01-07-Grafana-Install/image-20240618101706124.png)

<br/>

### **서비스 자동시작 활성화**

```bash
sudo systemctl enable grafana-server
```

<br/>

### **Ingress Rules 추가 (3000 포트)**

---

![image-20240618101824418](/../assets/img/posts/2024-01-07-Grafana-Install/image-20240618101824418.png)

<br/>

### **Grafana 대시보드 접속 (admin/admin)**

---

![image-20240618101920943](/../assets/img/posts/2024-01-07-Grafana-Install/image-20240618101920943.png)

<br/>
