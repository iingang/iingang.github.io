---
title: "[Linux 리눅스] iSCSI 개념 및 설치, 구성 방법 (RHEL 8.X)"
date: 2024-07-10 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, iscsi]
typora-root-url: ./
---

# **iSCSI 설치 및 구성**

---

## **iSCSI(아이스카시) 란?**

**iSCSI (Internet Small Computer System Interface)**는 TCP/IP 네트워크를 통해 스토리지 장치에 접근할 수 있게 해주는 스토리지 인터페이스다. 

iSCSI는 인터넷 또는 로컬 네트워크를 통해 원격 스토리지 장치에 접근하고 데이터를 전송할 수 있게 한다.

<br/>

**`iSCSI Target`**: 블록 장치를 공유할 수 있게 하는 스토리지 서버 **(iSCSI 서버)**

**`iSCSI Initiator`**: 원격 스토리지에 접근하는 클라이언트 **(iSCSI 클라이언트)**



<br/>

## **iSCSI 서버 구성**

---

### **1. targetcli 설치**

---

iSCSI 서버를 설정하고 관리하기 위해 **`targetcli`**를 설치하고 활성화한다.

- **`yum install targetcli`**
- **`systemctl enable --now target`**

<br/>

### **2. 방화벽 비활성화**

---

클라이언트에서 스토리지 접근을 위해 firewalld을 비활성화 한다. 

**`systemctl disable --now firewalld`**



<br/>

### **3. iSCSI 백업 저장소 확인**

---

targetcli를 실행하여 레이아웃을 확인해보자.

**`targetcli`**

```bash
o- / ..................................................................... [...]
  o- backstores .......................................................... [...]
  | o- block .............................................. [Storage Objects: 0]
  | o- fileio ............................................. [Storage Objects: 0]
  | o- pscsi .............................................. [Storage Objects: 0]
  | o- ramdisk ............................................ [Storage Objects: 0]
  o- iscsi ........................................................ [Targets: 0]
  o- loopback ..................................................... [Targets: 0]
/>
```

<br/>

iSCSI 백업 저장소에서 사용할 수 있는 저장소 유형은 다음과 같다.

- **`fileio`**: 로컬 파일 시스템에서 일반 파일을 디스크 이미지로 사용
- **`block`**: 로컬 블록 장치 및 논리적 장치를 사용
- **`pscsi`**: 스토리지 오브젝트가 SCSI 명령의 직접 패스스루를 지원
- **`ramdisk`**: 임시 RAM 백업 장치를 생성



<br/>

### **4. fileio 스토리지 오브젝트 생성**

---

오브젝트 생성을 위한 경로를 만들어준다.

targetcli에서 나와 디렉토리를 생성한다.

- **`exit`**
- **`mkdir /iSCSI`**



<br/>

**`/> backstores/fileio create name=fileio_ig file_or_dev=/iSCSI/fileio_ig size=10G`**

```bash
Created fileio fileio_ig with size 10737418240
```

<br/>

**`/> ls `**

```bash
o- / ..................................................................... [...]
  o- backstores .......................................................... [...]
  | o- block .............................................. [Storage Objects: 0]
  | o- fileio ............................................. [Storage Objects: 1]
  | | o- fileio_ig ........... [/iSCSI/fileio_ig (10.0GiB) write-back activated]
  | | | o- alua ............................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ................... [ALUA state: Active/optimized]
  | o- pscsi .............................................. [Storage Objects: 0]
  | o- ramdisk ............................................ [Storage Objects: 0]
  o- iscsi ........................................................ [Targets: 0]
  o- loopback ..................................................... [Targets: 0]
/>
```



<br/>

잘 생성된 것을 볼 수 있다.

```bash
[root@rhel88-iscsi ~]# ls -rtlh /iSCSI
total 0
-rw-r--r--. 1 root root 10.0G Jun 19 05:34 fileio_ig
```



<br/>

### **5. iSCSI 서버 Target 설정**

---

**`/> cd iscsi/`**

**`/iscsi> create wwn=iqn.2024-07.local.pacemaker.test:7900`**

```bash
Created target iqn.2024-07.local.pacemaker.test:7900.
Created TPG 1.
Global pref auto_add_default_portal=true
Created default portal listening on all IPs (0.0.0.0), port 3260.
```

<br/>

**`/iscsi> ls`**

```bash
o- iscsi .......................................................... [Targets: 1]
  o- iqn.2024-07.local.pacemaker.test:7900 ........................... [TPGs: 1]
    o- tpg1 ............................................. [no-gen-acls, no-auth]
      o- acls ........................................................ [ACLs: 0]
      o- luns ........................................................ [LUNs: 0]
      o- portals .................................................. [Portals: 1]
        o- 0.0.0.0:3260 ................................................... [OK]
```

<br/>

### **6. ACL 정책 설정**

---

특정 서버만 wwn에 접근할 수 있도록 ACL 설정을 해준다.

<br/>

생성한 target 경로로 이동한다.

**`/iscsi> cd iqn.2024-07.local.pacemaker.test:7900/tpg1`**

<br/>

2개 노드에 대한 ACL 설정을 한다.

**`/iscsi/iqn.20...est:7900/tpg1> acls/ create wwn=iqn.2024-07.com.pacemaker.test:7901`**

```bash
Created Node ACL for iqn.2024-07.com.pacemaker.test:7901
```

<br/>

**`/iscsi/iqn.20...est:7900/tpg1> acls/ create wwn=iqn.2024-07.com.pacemaker.test:7902`**

```bash
Created Node ACL for iqn.2024-07.com.pacemaker.test:7902
```

<br/>



### **7. LUN 생성 및 설정**

---

fileio_ig를 가진 LUN을 생성하여 지정된 클라이언트들이 접근할 수 있도록 연결한다.

**`/iscsi/iqn.20...est:7900/tpg1> luns/ create /backstores/fileio/fileio_ig`**

```bash
Created LUN 0.
Created LUN 0->0 mapping in node ACL iqn.2024-07.com.pacemaker.test:7902
Created LUN 0->0 mapping in node ACL iqn.2024-07.com.pacemaker.test:7901
```



<br/>

최상위 경로에서 생성된 것을 확인한다.

**`/iscsi/iqn.20...est:7900/tpg1> cd /`**

**`/> ls`**

```bash
o- / ..................................................................... [...]
  o- backstores .......................................................... [...]
  | o- block .............................................. [Storage Objects: 0]
  | o- fileio ............................................. [Storage Objects: 1]
  | | o- fileio_ig ........... [/iSCSI/fileio_ig (10.0GiB) write-back activated]
  | | | o- alua ............................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ................... [ALUA state: Active/optimized]
```

<br/>

### **8. 작업 저장**

---

**`/> exit`**

```bash
Global pref auto_save_on_exit=true
Last 10 configs saved in /etc/target/backup/.
Configuration saved to /etc/target/saveconfig.json
```

<br/>

### **9. iscsid 서비스 활성화**

---

**`systemctl enable --now iscsid`**

<br/>



## **iSCSI 서버 연결 (클라이언트 설정)**

---

사전에 pacemaker 구성을 해둔 RHEL88-01, RHEL88-02 서버에서 iSCSI 스토리지와 연결을 하려고 한다.

<br/>

### **1. initiatorname 설정**

---

**RHEL88-01 노드 설정**

**`vi /etc/iscsi/initiatorname.iscsi`**

```bash
InitiatorName=iqn.2024-07.com.pacemaker.test:7901
```

<br/>

**RHEL88-02 노드 설정**

**`vi /etc/iscsi/initiatorname.iscsi`**

```bash
InitiatorName=iqn.2024-07.com.pacemaker.test:7902
```

<br/>

### **2. iscsid 서비스 활성화**

---

iscsid 서비스를 클라이언트 2개 노드에 각각 활성화한다.

**`systemctl enable --now iscsid`**

<br/>

### **3. iSCSI Target 목록 검색**

---

**`iscsiadm -m discovery -t st -p [iSCSI 서버 IP]`**

```bash
[iSCSI 서버 IP]:3260,1 iqn.2024-07.local.pacemaker.test:7900
```

<br/>

### **4. iSCSI 디바이스 연결**

---

RHEL88-01, RHEL88-02 각 노드에서 iSCSI 서버와 연결한다.

**`iscsiadm -m node -T iqn.2024-07.local.pacemaker.test:7900 -p [iSCSI 서버 IP] -l`**

```bash
Logging in to [iface: default, target: iqn.2024-07.local.pacemaker.test:7900, portal: [iSCSI 서버 IP],3260] (multiple)
Login to [iface: default, target: iqn.2024-07.local.pacemaker.test:7900, portal: [iSCSI 서버 IP],3260] successful.
```

<br/>

### **5. 연결 확인**

---

iSCSI 서버가 sdb 블록 장치로 연결된 것을 볼 수 있다.

**`lsblk`**

```bash
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0  100G  0 disk
├─sda1          8:1    0  600M  0 part /boot/efi
├─sda2          8:2    0    1G  0 part /boot
└─sda3          8:3    0 98.4G  0 part
  ├─rhel-root 253:0    0 85.5G  0 lvm  /
  ├─rhel-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─rhel-home 253:2    0    5G  0 lvm  /home
sdb             8:16   0    5G  0 disk
sr0            11:0    1 11.7G  0 rom  /media
```

<br/>

**`lsscsi -l`**

```bash
[0:0:0:0]    disk    VMware   Virtual disk     2.0   /dev/sda
  state=running queue_depth=254 scsi_level=7 type=0 device_blocked=0 timeout=180
[3:0:0:0]    cd/dvd  NECVMWar VMware SATA CD00 1.00  /dev/sr0
  state=running queue_depth=1 scsi_level=6 type=5 device_blocked=0 timeout=30
[33:0:0:0]   disk    LIO-ORG  fileio_ig     4.0   /dev/sdb
  state=running queue_depth=32 scsi_level=7 type=0 device_blocked=0 timeout=30
```



<br/>

## **References**

---

<https://docs.redhat.com/ko/documentation/red_hat_enterprise_linux/8/html/managing_storage_devices/creating-an-iscsi-target_configuring-an-iscsi-target>
