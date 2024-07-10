---
title: "[Pacemaker/RHEL 8] Pacemaker 설치 및 클러스터 구성"
date: 2024-07-02 18:00:00 +0900
categories: [Clustering, Pacemaker]
tags: [pacemaker, RHEL, RedHat]
typora-root-url: ./
---

# **Pacemaker Install Guide**

---



### **Test Environment**

{: .prompt-info }
> Red Hat Enterprise Linux release 8.8 



<br/>

## **1. 사전 환경설정 (모든 노드에서 입력)**

---



### **1) 방화벽 비활성화**

---

- **`systemctl disable --now firewalld`**

<br/>

### **2) SELinux 비활성화**

---

- **`setenforce 0`**
- **`sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config`**



<br/>

### 3) **RHEL 8.8 High-Availability DVD ISO 파일 Download**

---

아래 링크에서 **`Red Hat Enterprise Linux 8.8 Binary DVD`** 파일을 다운로드한다. (Red Hat 계정 필요)

<https://access.redhat.com/downloads/content/83/ver=/rhel---8/8.8/x86_64/product-software>

![image-20240708124430863](/../assets/img/posts/2024-07-02-Pacemaker-Install/image-20240708124430863.png)



<br/>

### **4) /etc/hosts 파일 수정**

---

클러스터로 묶을 노드의 ip 주소와 호스트명을 작성할 것이다.

**`Main IP`**, **`Heartbeat IP`** 이렇게 각각 2개씩 등록한다. 

main ip는 클러스터 운영, 애플리케이션 서비스 제공, 데이터 통신 등을 위한 주 네트워크이다.

heartbeat ip는 HA Cluster 노드 간의 상태 확인과 같은 내부 통신을 위한 전용 네트워크이다.

2개를 동일한 ip로 구성할 수 있지만 독립적으로 운영하는 것이 고가용성, 신뢰성 향상 측면에서 이상적이다.

여기서는 테스트 환경이므로 동일 ip로 구성하겠다.

**`vi /etc/hosts`**

```bash
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.16.0.145 rhel88-01
172.16.0.146 rhel88-02

172.16.0.145 rhel88-01-hb
172.16.0.146 rhel88-02-hb
```

<br/>

### **5) /etc/kdump.conf 파일 수정**

---

**`fence_kdump_args`** 설정을 주석 해제하고, **`fence_kdump_nodes`** 노드 이름을 변경해준다.

**`vi /etc/kdump.conf`**

```bash
..skip..
#raw /dev/vg/lv_kdump
#ext4 /dev/vg/lv_kdump
#ext4 LABEL=/boot
#ext4 UUID=03138356-5e61-4ab3-b58e-27507ac41937
#nfs my.server.com:/export/tmp
#nfs [2001:db8::1:2:3:4]:/export/tmp
#ssh user@my.server.com
#ssh user@2001:db8::1:2:3:4
#sshkey /root/.ssh/kdump_id_rsa
path /var/crash
core_collector makedumpfile -l --message-level 7 -d 31
#core_collector scp
#kdump_post /var/crash/scripts/kdump-post.sh
#kdump_pre /var/crash/scripts/kdump-pre.sh
#extra_bins /usr/bin/lftp
#extra_modules gfs2
#failure_action shell
#force_rebuild 1
#force_no_rebuild 1
#dracut_args --omit-drivers "cfg80211 snd" --add-drivers "ext2 ext3"
fence_kdump_args -p 7410 -f auto -c 0 -i 10 # 주석 해제
#fence_kdump_nodes node1 node2
fence_kdump_nodes rhel88-02-hb # 상대 노드로 변경
```

<br/>

## **2. Pacemaker 설치 (모든 노드에서 입력)**

---

### **1) ISO 파일 마운트**

---

다운로드 받은 ISO 파일을 SFTP를 이용해 각 노드에 업로드한다.

이제 ISO 파일을 마운트하여 레파지토리에 등록할 것이다.

여기서는 /root 경로에 업로드된 iso 파일을 /mnt 경로로 마운트했다. (폴더가 없으면 생성 필요)

- **`mount /dev/sr0 /media`**
- **`mount /rhel-highavailability-8.8-x86_64-dvd.iso /mnt`**

<br/>

### **2) Local Repository 수정**

---

**`vi /etc/yum.repos.d/local.repo`**

```bash
[BaseOS]
name=BaseOS
baseurl=file:///media/BaseOS
enabled=1
gpgcheck=0

[AppStream]
name=AppStream
baseurl=file:///media/AppStream
enabled=1
gpgcheck=0

[HA]
name=HA
baseurl=file:///mnt/
enabled=0
gpgcheck=0
```



<br/>

### **3) Yum Cache 정리 / Repository List 확인**

---

yum의 캐시된 데이터를 정리한다. (ex. 다운로드된 패키지 정보, 만료된 캐시 데이터 등)

- **`yum clean all`**

<br/>

yum이 인식하고 있는 모든 repository의 리스트를 출력한다.

위에서 등록한 3개의 repository를 잘 인식하고 있는지 확인이 필요하다.

- **`yum repolist`**



<br/>

### **4) Pacemaker 관련 패키지 설치**

---

클러스터 관리 도구(pcs), 리소스 매니저(pacemaker), 그리고 다양한 fencing 에이전트(fence-agents-all)를 설치하여 고가용성 클러스터 환경을 구성하고 관리할 수 있도록 한다.

- **`yum install pcs pacemaker fence-agents-all`**

<br/>



### **5) pcsd 서비스 활성화**

---

**`pcsd`** 서비스를 부팅 시 자동으로 시작하도록 설정하며, 현재 세션에서 즉시 시작하도록 하는 명령어를 입력한다.

- **`systemctl enable --now pcsd`**



<br/>



## **3. Pacemaker Cluster 구성**

---

### **1) HA Cluster 사용자 비밀번호 설정 (모든 노드에서 입력)**

---

모든 노드에서 비밀번호 설정을 한다.

**`echo [password] | passwd hacluster --stdin`**



<br/>

### **2) 노드 인증 (1번 노드에서만 입력)**

**`pcs host auth -u hacluster -p [password] rhel88-01-hb rhel88-02-hb`**

다음과 같이 인증되었다고 출력된다.

```bash
rhel88-01-hb: Authorized
rhel88-02-hb: Authorized
```

<br/>



### **3) Pacemaker 클러스터 설정  (1번 노드에서만 입력)**

---

pacemaker 클러스터 이름을 **`rhelha`**로 설정하고, 각 노드의 주소를 지정한다.

**`pcs cluster setup rhelha rhel88-01-hb addr=172.16.0.145 addr=172.16.0.145 rhel88-02-hb addr=172.16.0.146 addr=172.16.0.146`**

```bash
Destroying cluster on hosts: 'rhel88-01-hb', 'rhel88-02-hb'...
rhel88-01-hb: Successfully destroyed cluster
rhel88-02-hb: Successfully destroyed cluster
Requesting remove 'pcsd settings' from 'rhel88-01-hb', 'rhel88-02-hb'
rhel88-01-hb: successful removal of the file 'pcsd settings'
rhel88-02-hb: successful removal of the file 'pcsd settings'
Sending 'corosync authkey', 'pacemaker authkey' to 'rhel88-01-hb', 'rhel88-02-hb'
rhel88-01-hb: successful distribution of the file 'corosync authkey'
rhel88-01-hb: successful distribution of the file 'pacemaker authkey'
rhel88-02-hb: successful distribution of the file 'corosync authkey'
rhel88-02-hb: successful distribution of the file 'pacemaker  authkey'
Sending 'corosync.conf' to 'rhel88-01-hb', 'rhel88-02-hb'
rhel88-01-hb: successful distribution of the file 'corosync.conf'
rhel88-02-hb: successful distribution of the file 'corosync.conf'
Cluster has been successfully set up.
```

<br/>

### **4) Pacemaker 클러스터 실행 (1번 노드에서만 입력)**

---

Pacemaker 클러스터의 모든 노드에서 클러스터 서비스를 시작하는 명령어를 입력한다.

**`pcs cluster start --all`**

```bash
Cluster name: rhelha

WARNINGS:
No stonith devices and stonith-enabled is not false

Status of pacemakerd: 'Pacemaker is running' (last updated 2024-07-02 14:00:27 +09:00)
Cluster Summary:
  * Stack: corosync
  * Current DC: rhel88-02-hb (version 2.1.5-8.el8-a3f44794f94) - partition with quorum
  * Last updated: Tue Jul  2 14:00:27 2024
  * Last change:  Tue Jun  2 12:20:08 2024 by root via cibadmin on rhel88-01-hb
  * 2 nodes configured
  * 0 resource instances configured

Node List:
  * Online: [ rhel88-01-hb rhel88-02-hb ]

Full List of Resources:
  * No resources

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```

<br/>

