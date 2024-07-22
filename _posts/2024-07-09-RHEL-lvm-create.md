---
title: "[Linux 리눅스] LVM 볼륨 수동 생성 방법 (RHEL 7 Pacemaker 사용 환경)"
date: 2024-07-09 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, LVM, pacemaker]
typora-root-url: ./
---

# **LVM 볼륨 수동으로 생성하기**

---

{: .prompt-info }
> Red Hat Enterprise Linux Server release 7.9 X 2대 
>
> RHEL High Availability Cluster(Pacemaker) 사용 환경

**`pcs status`**

```bash
Cluster name: rhel-ha
Stack: corosync
Current DC: test02-hb (version 1.1.23-1.el7-9acf116022) - partition with quorum
Last updated: Wed Jul 10 11:33:09 2024
Last change: Tue Jul  9 16:27:06 2024 by root via crm_resource on test01-hb

2 nodes configured
12 resource instances configured

Online: [ test01-hb test02-hb ]

Full list of resources:

 fence_vmware-01        (stonith:fence_vmware_rest):    Started test02-hb
 fence_vmware-02        (stonith:fence_vmware_rest):    Started test01-hb
 Resource Group: rhelha
     havg       (ocf::heartbeat:LVM):   Started test01-hb
     halv       (ocf::heartbeat:Filesystem):    Started test01-hb
     ha_vip     (ocf::heartbeat:IPaddr2):       Started test01-hb
 Resource Group: rhelha2
     havg2      (ocf::heartbeat:LVM):   Started test02-hb
     halv2      (ocf::heartbeat:Filesystem):    Started test02-hb
     ha_vip2    (ocf::heartbeat:IPaddr2):       Started test02-hb
 Clone Set: ping-clone [ping]
     Started: [ test01-hb test02-hb ]

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```



<br/>

## **lvm2-lvmetad 서비스 비활성화**

---

**`lvm2-lvmetad`** 는 LVM에서 사용하는 데몬으로, LVM 메타데이터를 캐시하여 LVM 명령어의 성능을 향상시키고 여러 LVM 작업 간에 메타데이터 일관성을 유지하도록 한다.

해당 서비스를 **비활성화**하는 이유는, Pacemaker 사용 환경에서 **공유 볼륨 변경사항이 정상적으로 반영**되도록 하기 위해서이다. 

<br/>

먼저 아래 파일에서 **use_lvmetad = 1** 로 되어있는 설정을 **<span style='background-color:#fff5b1'>0으로 변경</span>**하고 저장한다.

**`vi /etc/lvm/lvm.conf`**

```bash
use_lvmetad = 0
```



<br/>

lvm2-lvmetad 서비스를 중지 및 비활성화한다.

**`systemctl disable --now lvm2-lvmetad`**

```bash
systemctl disable --now lvm2-lvmetad
```



<br/>

잘 중지되었는지 서비스 상태를 확인한다.

**` systemctl status lvm2-lvmetad`**

```bash
● lvm2-lvmetad.service - LVM2 metadata daemon
   Loaded: loaded (/usr/lib/systemd/system/lvm2-lvmetad.service; static; vendor preset: enabled)
   Active: inactive (dead) since Tue 2024-07-09 15:51:23 KST; 2s ago
     Docs: man:lvmetad(8)
  Process: 621 ExecStart=/usr/sbin/lvmetad -f (code=exited, status=0/SUCCESS)
 Main PID: 621 (code=exited, status=0/SUCCESS)

Jul 01 14:08:36 sslifeha001 systemd[1]: Started LVM2 metadata daemon.
Jul 09 15:51:23 sslifeha001 systemd[1]: Stopping LVM2 metadata daemon...
Jul 09 15:51:23 sslifeha001 systemd[1]: Stopped LVM2 metadata daemon.
```



<br/>



## **LVM 볼륨 생성**

---

현재 iSCSI 서버를 별도로 구성하여 공유 스토리지로 사용하는 중이다.

볼륨을 확장하기 위해서 기존 사용중인 iSCSI 서버에 새로운 스토리지 작업을 추가해줘야 하며, 별도 [포스팅](https://iingang.github.io/posts/RHEL-iSCSI/)을 참고하길 바란다.

이 테스트를 위해 10G의 새로운 스토리지를 iSCSI 서버에 추가하였다.

<br/>

현재 블록 장치 상태를 확인해보자.

**`lsblk`**

```bash
NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda             8:0    0  100G  0 disk
├─sda1          8:1    0    1G  0 part /boot
└─sda2          8:2    0   99G  0 part
  ├─rhel-root 253:0    0   50G  0 lvm  /
  ├─rhel-swap 253:1    0  7.9G  0 lvm  [SWAP]
  └─rhel-home 253:2    0 41.1G  0 lvm  /home
sdb             8:16   0   10G  0 disk
sdc             8:32   0   10G  0 disk
└─havg-halv   253:3    0   10G  0 lvm  /mount
sdd             8:48   0   10G  0 disk
sr0            11:0    1  4.2G  0 rom
```

<br/>

현재 물리 볼륨 상태를 확인해보자.

**`pvs`**

```bash
  PV         VG    Fmt  Attr PSize   PFree
  /dev/sda2  rhel  lvm2 a--  <99.00g 4.00m
  /dev/sdb   havg2 lvm2 a--    9.99g    0
  /dev/sdc   havg  lvm2 a--    9.99g    0
```

이 상태에서 새로운 LVM 볼륨을 만들어보자. 

<br/>

### **1. 물리 볼륨(PV) 생성**

---

새로 생성한 공유 스토리지 /dev/sdd를 pv로 생성한다.

**`pvcreate /dev/sdd`**

```bash
Physical volume "/dev/sdd" successfully created.
```

<br/>

### **2. 볼륨 그룹(VG) 생성**

---

생성한 pv에 대한 havg3 볼륨 그룹을 생성한다.

**`vgcreate havg3 /dev/sdd`**

```bash
Volume group "havg3" successfully created
```

<br/>

볼륨 그룹이 잘 생성되었는지 확인해보자.

**`vgs -o+tags`**

```bash
  VG    #PV #LV #SN Attr   VSize   VFree VG Tags
  havg    1   1   0 wz--n-   9.99g    0  pacemaker
  havg2   1   1   0 wz--n-   9.99g    0  pacemaker
  havg3   1   0   0 wz--n-   9.99g 9.99g
  rhel    1   3   0 wz--n- <99.00g 4.00m
```

<br/>



### **3. 논리 볼륨(LV) 생성**

---

**`lvcreate --name=halv3 -l +100%FREE havg3`**

```bash
Volume "havg3/halv3" is not active locally (volume_list activation filter?).
Aborting. Failed to wipe start of new LV.
```

논리 볼륨을 생성하려고 하니 오류가 발생한다.

그 이유는**`/etc/lvm/lvm.conf`** 파일에서 pacemaker를 구축할 때 **volume_list = [ "rhel" ]** 라고 설정하였기 때문이다. 

위 설정을 해준 이유는 LVM이 인식하고 관리할 볼륨 그룹의 목록을 **`rhel`**로 특정하여 주요 파티션은 로컬에서 관리하기 위해서이다.  **sda** 디스크 장치를 **`rhel`** 볼륨 그룹이 관리하는 것을 알 수 있다. (pvs, lsblk 명령어로 확인)

default 설정은 volume_list 옵션이 비활성화 되어있다.

<br/>

현재 **`volume_list`** 옵션으로 인해 로컬 환경에서 LV 생성이 불가하므로, havg3을 페이스메이커에 의해 관리될 수 있게 **`pacemaker`** 태그를 붙여서 LV를 생성해보자.



<br/>

생성된 havg3에 pacemaker 태그를 추가한다.

 **`vgchange --addtag pacemaker havg3`**

```bash
Volume group "havg3" successfully changed
```



<br/>

잘 추가된 것을 확인할 수 있다.

**`vgs -o+tags`**

```bash
  VG    #PV #LV #SN Attr   VSize   VFree VG Tags
  havg    1   1   0 wz--n-   9.99g    0  pacemaker
  havg2   1   1   0 wz--n-   9.99g    0  pacemaker
  havg3   1   0   0 wz--n-   9.99g 9.99g pacemaker
  rhel    1   3   0 wz--n- <99.00g 4.00m
```



임시로 volume_list에 pacemaker를 삽입하여 havg3을 Pacemaker 클러스터에 의해 관리되는 볼륨 그룹으로 활성화하기 위한 명령어를 입력한다.

현재 havg3으로 만들어진 LV가 없으므로 0 logical volume 이라고 출력된다.

**`vgchange -ay havg3 --config 'activation { volume_list = ["@pacemaker" ]}'`**

```bash
0 logical volume(s) in volume group "havg3" now active
```

<br/>

위의 오류가 발생한 LV 생성 명령어에서 **volume_list**에 **pacemaker 태그를 추가**하여 다시 생성해보자.

**`lvcreate --name=halv3 -l +100%FREE havg3 --config 'activation { volume_list = [ "@pacemaker" ]}'`**

```bash
Logical volume "halv3" created.
```

<br/>

LV가 잘 생성된 것을 볼 수 있다.

**`lvs`**

```bash
  LV    VG    Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  halv  havg  -wi-ao----   9.99g                                                
  halv2 havg2 -wi-------   9.99g                                                
  halv3 havg3 -wi-a-----   9.99g                                                
  home  rhel  -wi-ao---- <41.12g                                                
  root  rhel  -wi-ao----  50.00g                                                
  swap  rhel  -wi-ao----  <7.88g     
```

<br/>

생성한 논리 볼륨(LV)을 포맷한다.

**`mkfs.xfs /dev/havg3/halv3`**

```bash
meta-data=/dev/havg3/halv3       isize=512    agcount=4, agsize=654848 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0, sparse=0
data     =                       bsize=4096   blocks=2619392, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

<br/>

생성하기 위해 임시로 붙여줬던 태그를 제거해준다.

**`vgchange --deltag pacemaker havg3`**

```bash
Volume group "havg3" successfully changed
```

<br/>

잘 제거된 것을 볼 수 있다.

**`vgs -o+tags`**

```bash
  VG    #PV #LV #SN Attr   VSize   VFree VG Tags
  havg    1   1   0 wz--n-   9.99g    0  pacemaker
  havg2   1   1   0 wz--n-   9.99g    0  pacemaker
  havg3   1   1   0 wz--n-   9.99g    0
  rhel    1   3   0 wz--n- <99.00g 4.00m
```



<br/>

### **Pacemaker Resource 생성 (LVM, FileSystem)**

---

생성한 LVM 볼륨을 pcs 리소스로 등록해준다.

**`pcs resource create havg3 LVM volgrpname=havg3 exclusive=true --group rhelha3`**

```bash
Assumed agent name 'ocf:heartbeat:LVM' (deduced from 'LVM')
```

<br/>

**`pcs resource create halv3 Filesystem device=/dev/havg3/halv3 directory=/mount3 fstype=xfs run_fsck=no --group rhelha3`**

```bash
Assumed agent name 'ocf:heartbeat:Filesystem' (deduced from 'Filesystem')
```

<br/>

페이스메이커 클러스터에 리소스가 잘 등록된 것을 볼 수 있다.

**`pcs status`**

```bash
Stack: corosync
Current DC: test02-hb (version 1.1.23-1.el7-9acf116022) - partition with quorum
Last updated: Wed Jul 10 12:14:02 2024
Last change: Tue Jul  9 16:27:06 2024 by root via crm_resource on test01-hb

2 nodes configured
12 resource instances configured

Online: [ test01-hb test02-hb ]

Full list of resources:

 fence_vmware-01        (stonith:fence_vmware_rest):    Started test02-hb
 fence_vmware-02        (stonith:fence_vmware_rest):    Started test01-hb
 Resource Group: rhelha
     havg       (ocf::heartbeat:LVM):   Started test01-hb
     halv       (ocf::heartbeat:Filesystem):    Started test01-hb
     ha_vip     (ocf::heartbeat:IPaddr2):       Started test01-hb
 Resource Group: rhelha2
     havg2      (ocf::heartbeat:LVM):   Started test02-hb
     halv2      (ocf::heartbeat:Filesystem):    Started test02-hb
     ha_vip2    (ocf::heartbeat:IPaddr2):       Started test02-hb
 Clone Set: ping-clone [ping]
     Started: [ test01-hb test02-hb ]
 Resource Group: rhelha3
     havg3      (ocf::heartbeat:LVM):   Started test02-hb
     halv3      (ocf::heartbeat:Filesystem):    Started test02-hb

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
```



<br/>

## **References**

---

<https://access.redhat.com/solutions/2682031>

<br/>
