---
title: "[Grafana 그라파나] Grafana SMTP 메일서버 구성 / Email Alert(이메일 알림) 설정 (Oracle Linux)"
date: 2024-05-02 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Enterprise]
typora-root-url: ./
---

## **SMTP 테스트 서버 구축**

---

{: .prompt-info}

> OS: Oracle Linux 8.8
>
> Version: Grafana Enterprise 10.1.2

<br/>

### **sendmail 설치**

---

yum으로 sendmail 관련 패키지를 설치한다.

**`yum install -y sendmail sendmail-cf m4`**

<br/> 잘 설치되었는지 확인한다.

**` rpm -qa |grep sendmail`**

```bash
sendmail-8.15.2-34.0.1.el8.x86_64
sendmail-cf-8.15.2-34.0.1.el8.noarch
```



<br/>

### **sendmail.mc 파일 수정**

---

아래 내용을 모두 dnl 제거(주석 제거)하고, 모든 네트워크 인터페이스에서 이메일을 수신하도록 Addr은 0.0.0.0으로 변경하여 파일을 저장한다.

**`vi /etc/mail/sendmail.mc`** 

```bash
TRUST_AUTH_MECH(`EXTERNAL DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')dnl
define(`confAUTH_MECHANISMS', `EXTERNAL GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')dnl

DAEMON_OPTIONS(`Port=smtp,Addr=0.0.0.0, Name=MTA')dnl
DAEMON_OPTIONS(`Port=submission, Name=MSA, M=Ea')dnl
```

{: .prompt-tip}

> **TRUST_AUTH_MECH 및 confAUTH_MECHANISMS**: sendmail 인증 매커니즘 정의를 통한 보안 향상
>
> **DAEMON_OPTIONS**: Sendmail 데몬이 어떤 포트와 주소에서 이메일을 수신할지를 정의

<br/>

### **sendmail.cf 파일 생성**

---

mc 파일을 cf 파일로 변환한다.

**`m4 sendmail.mc > sendmail.cf`**



<br/>

### **/etc/hosts 파일 수정**

---

기존 ip, hostname 정보 뒷줄에 mail을 추가한다.

```bash
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 mail

192.168.56.2 grafana-server mail
```





<br/>

### **sendmail 서비스 시작**

---

**`systemctl enable --now sendmail`**

**`systemctl status sendmail`**

```bash
● sendmail.service - Sendmail Mail Transport Agent
   Loaded: loaded (/usr/lib/systemd/system/sendmail.service; disabled; vendor preset: disabled)
   Active: active (running) since Thu 2024-05-02 07:03:02 GMT; 2 months 22 days ago
 Main PID: 956254 (sendmail)
    Tasks: 1 (limit: 150412)
   Memory: 3.5M
   CGroup: /system.slice/sendmail.service
           └─956254 sendmail: accepting connections
```



<br/>

## **Grafana 파일 수정 및 대시보드 설정**

### **grafana defaults.ini 파일 수정**

---

**`vi ${GRAFANA_HOME}/conf/defaults.ini`**

```bash
[smtp]
enabled = true
host = smtp.gmail.com:587 ## gmail 사용
user = "XXX@gmail.com"  ## SMTP 서버에 인증하기 위해 사용하는 주소로 이메일을 발송할 Gmail 계정
# If the password contains # or ; you have to wrap it with triple quotes. Ex """#password;"""
password = "xxxx xxxx xxxx xxxx" ## 구글 계정 앱 비밀번호 설정필요
#cert_file =
#key_file =
skip_verify = true
from_address = "XXX@gmail.com" ##  이메일의 "발신자" 필드에 표시될 이메일 주소
from_name = Grafana
```

<br/>



### **Grafana 서버 재기동**

---

그라파나 서버를 재기동한다.

**`./bin/grafana server --config ${GRAFANA_HOME}/conf/defaults.ini &`**

<br/>



### **Grafana Email Alert 보내기**

---

**[Alerting] > [Contact points] > [Add contact point]** 메뉴에서 이메일 설정을 추가한다.

![image-20240725101333321](/../assets/img/posts/2024-05-02-Grafana-smtp/image-20240725101333321.png)

<br/>

Test 알림을 보내면 **Addresses에 입력된 이메일주소(수신자)**로 알림이 전송된다.

![image-20240725101713341](/../assets/img/posts/2024-05-02-Grafana-smtp/image-20240725101713341.png)

<br/>

### **이메일 수신 확인**

---

설정한 **from_address의 이메일 계정(발신)**에서 **user 이메일 계정(수신)**으로 이벤트 알림전송이 다음과 같이 잘 오는지 확인하면 된다.

![image-20240725102226696](/../assets/img/posts/2024-05-02-Grafana-smtp/image-20240725102226696.png)

<br/>



## **References**

---

<https://grafana.com/docs/grafana/latest/alerting/configure-notifications/manage-contact-points/integrations/configure-email/>

<https://yt16.tistory.com/105>
