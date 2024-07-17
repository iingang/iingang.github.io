---
title: "[Linux/RHEL] RHEL 9 Installation Guide"
date: 2024-07-15 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL]
typora-root-url: ./
---



# **RHEL 9 설치 방법**

---

### **Test Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 9.4 (Plow)
>
> VMware vSphere
>
> Red Hat Enterprise Linux 9.4 Binary DVD



<br/>

### **0. 언어 선택**

---

English로 선택해준다.

![image-20240715144913365](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715144913365.png)



### **1. RHEL 9.4 설치 화면**

---

언어를 선택하면 9.4 설치 요약화면을 볼 수 있다.

![image-20240715145019409](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145019409.png)





<br/>

### **2. TIME & DATE 선택**

---

**Asia(아시아) -  Seoul(서울)**을 입력해준다. (지도상 클릭 가능)

![image-20240715145052613](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145052613.png)



<br/>

### **3. Network 설정**

---

Manual 세팅으로 원하는 ip로 설정해준다.

![image-20240715145306183](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145306183.png)

<br/>

### **4. 호스트명 수정**

---

좌측 하단 호스트이름을 변경하고 Apply 버튼을 누르면 우측 하단에 잘 반영된 것을 확인할 수 있다.

![image-20240715145435628](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145435628.png)



<br/>

### **5. 디스크 구성**

---

자동으로 구성해도 되지만, 파티션 용량을 변경하기 위해 Custom으로 진행한다.

![image-20240715145601322](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145601322.png)

<br/>

**`Click here to create them automatically`**를 누르면 우선 기본 세팅이 보여진다.

![image-20240715145715383](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145715383.png)

<br/>

/home 디렉토리의 default 세팅 크기는 크기 때문에 5GB로 변경하여 우측 하단 Update Settings를 눌러 적용해준다.

![image-20240715145920800](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145920800.png)

<br/>

추가로 생긴 용량을 / 공간에 할당해준다.

현재 사용 가능한 용량은 좌측 하단 빨간색 AVAILABLE SPACE 에서 확인할 수 있다.

![image-20240715145958655](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715145958655.png)

<br/>

변경사항을 수락하고 진행한다.

![image-20240715150022892](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715150022892.png)

<br/>

### **6. Root 비밀번호 변경**

---

root password 설정을 추가해준다. 

SSH 로그인을 사용하기 위해 **`Allow root SSH login with password`**를 체크한다. 

![image-20240715150140002](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715150140002.png)



<br/>

필수 설정을 다 적용하면 Begin Installation 버튼이 활성화 되었을 것이다.

그대로 클릭하여 진행한다.

![image-20240715150222143](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715150222143.png)

<br/>

### **7. 시스템 재부팅**

---

설치가 완료되면, 설치 시작 버튼 위치에 Reboot 버튼이 활성화 되었을 것이다.

시스템 재부팅을 진행하면 된다.

<br/>

### **8. CLI 환경 진입**

---

시스템이 실행되면 **`Ctrl + Alt + F2`**를 눌러 cli 환경으로 바로 진입한다.

설정한 root 계정으로 로그인하여 위에서 설정한 내용이 잘 적용되었는지 확인해본다.

ex) **`hostname`**, **`ifconfig`**

![image-20240715152734349](/../assets/img/posts/2024-07-15-RHEL-9-Install/image-20240715152734349.png)

<br/>

### **9. 네트워크 설정 변경 시, 사용** 

---

네트워크 설정을 추가하거나 변경한 후에 터미널 끊김 없이 특정 네트워크 인터페이스 카드(NIC) 연결을 재시작 하는 명령어이다.

```bash
nmcli con down [NIC] && nmcli con up [NIC]
```

<br/>

**`ifconfig`** 명령어를 입력했을 때 NIC는 **`ens192`** 임을 확인할 수 있다.

```bash
ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.0.170  netmask 255.255.255.0  broadcast 172.16.0.255
        inet6 fe80::250:56ff:febc:8cf1  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:bc:8c:f1  txqueuelen 1000  (Ethernet)
        RX packets 1227289  bytes 815336754 (777.5 MiB)
        RX errors 0  dropped 8  overruns 0  frame 0
        TX packets 246662  bytes 17936086 (17.1 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```



<br/>



### **10. 영구 마운트 등록**

---

**`blkid`** 명령을 통해 현재 사용 중인 iso 파일의 라벨명을 확인할 수 있다.

```bash
/dev/sr0: UUID="2024-04-12-03-32-56-00" LABEL="RHEL-9-4-0-BaseOS-x86_64" TYPE="iso9660" PTUUID="8b7567d5" PTTYPE="dos"
```



<br/>

시스템 재부팅 시에도 항상 반영될 수 있도록, fstab 파일에 라벨명으로 /media 위치에 영구 마운트 설정을 해준다.

**`vi /etc/fstab`**

```bash
LABEL=RHEL-9-4-0-BaseOS-x86_64 /media iso9660   defaults        0 0
```

<br/>

**`mount -a`** 명령으로 **`/etc/fstab`** 파일에 정의된 모든 파일 시스템을 마운트한다.

```bash
mount: /media: WARNING: source write-protected, mounted read-only.
mount: (hint) your fstab has been modified, but systemd still uses
       the old version; use 'systemctl daemon-reload' to reload.
```



<br/>

systemd 데몬이 현재 사용 중인 설정 파일들을 다시 업데이트하도록 명령어를 입력한다.

**`systemctl daemon-reload`**

<br/>

**`lsblk`** 명령어로 /media 위치에 마운트 된 것을 확인할 수 있다.

```bash
NAME                  MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                     8:0    0   500G  0 disk
├─sda1                  8:1    0   600M  0 part /boot/efi
├─sda2                  8:2    0     1G  0 part /boot
└─sda3                  8:3    0 497.9G  0 part
  ├─rhel_bastion-root 253:0    0   485G  0 lvm  /
  ├─rhel_bastion-swap 253:1    0   7.9G  0 lvm  [SWAP]
  └─rhel_bastion-home 253:2    0     5G  0 lvm  /home
sr0                    11:0    1  10.3G  0 rom  /media
```

<br/>
