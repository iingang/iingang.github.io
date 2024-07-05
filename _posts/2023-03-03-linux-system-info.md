---
title: "[Linux/RHEL 8] 리눅스 시스템 정보 확인 (CPU, Memory, Disk, LVM)"
date: 2023-03-03 00:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL]
---



# **Linux System Info**

---



## **1. CPU 정보 확인**

---

시스템 전반에 대한 정보를 나타냅니다.

**`# dmidecode -t system`**

```bash
# dmidecode 3.3
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0001, DMI type 1, 27 bytes
System Information
        Manufacturer: VMware, Inc. # 시스템 제조사
        Product Name: VMware7,1 # 제품 이름
        Version: None # 제품 버전
        Serial Number: VMware-00 aa bb cc dd ee ff gg # 일련번호
        UUID: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx # 시스템의 UUID
        Wake-up Type: Power Switch
        SKU Number: Not Specified
        Family: Not Specified

Handle 0x009A, DMI type 23, 13 bytes
System Reset
        Status: Enabled
        Watchdog Timer: Present
        Boot Option: Do Not Reboot
        Boot Option On Limit: Do Not Reboot
        Reset Count: Unknown
        Reset Limit: Unknown
        Timer Interval: Unknown
        Timeout: Unknown

Handle 0x009D, DMI type 32, 11 bytes
System Boot Information
        Status: No errors detected
```

<br/>

시스템의 프로세서 관련 정보를 나타냅니다.

**`# dmidecode -t processor`**

```bash
# dmidecode 3.3
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0004, DMI type 4, 48 bytes
Processor Information
        Socket Designation: CPU 0 # 프로세서가 장착된 소켓의 위치
        Type: Central Processor # 프로세서 유형
        Family: Unknown # 프로세서 계열
        Manufacturer: GenuineIntel # 프로세서 제조사
        ID: 00 11 22 33 AA BB CC DD EE FF
        Version: Intel(R) Xeon(R) Silver 4214 CPU @ 2.20GHz
        Voltage: 3.3 V
        External Clock: Unknown
        Max Speed: 2098 MHz
        Current Speed: 2098 MHz
        Status: Populated, Enabled
        Upgrade: ZIF Socket
        L1 Cache Handle: Not Provided
        L2 Cache Handle: Not Provided
        L3 Cache Handle: Not Provided
        Serial Number: Not Specified
        Asset Tag: Not Specified
        Part Number: Not Specified
        Core Count: 1
        Core Enabled: 1
        Characteristics:
                64-bit capable
                Execute Protection
```

<br/>

현재 인식된 CPU 정보를 출력하는 명령어입니다. CPU 코어에 대한 세부 정보가 표시됩니다.

**`# cat /proc/cpuinfo`**

```bash
processor       : 0 # CPU 코어번호
vendor_id       : GenuineIntel # CPU 제조사
cpu family      : 6
model           : 85
model name      : Intel(R) Xeon(R) Silver 4214 CPU @ 2.20GHz
stepping        : 7
microcode       : 0xxxxxxxx
cpu MHz         : 2199.999 # 현재 CPU 클럭 속도
cache size      : 16896 KB # CPU 캐시 크기
physical id     : 0 # 물리적인 CPU 패키지 ID
siblings        : 1
core id         : 0
cpu cores       : 1
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 22
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon nopl xtopology tsc_reliable nonstop_tsc cpuid pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch invpcid_single ssbd ibrs ibpb stibp ibrs_enhanced fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid avx512f avx512dq rdseed adx smap clflushopt clwb avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves arat pku ospke avx512_vnni md_clear flush_l1d arch_capabilities
bugs            : spectre_v1 spectre_v2 spec_store_bypass swapgs itlb_multihit mmio_stale_data retbleed eibrs_pbrsb # CPU가 지원하는 기능들의 리스트
bogomips        : 4399.99
clflush size    : 64
cache_alignment : 64
address sizes   : 45 bits physical, 48 bits virtual
power management:
```



<br/>

마찬가지로 CPU 및 관련 정보를 요약해서 출력해주는 명령어입니다.

**`# lscpu`**

```bash
Architecture:        x86_64
CPU op-mode(s):      32-bit, 64-bit
Byte Order:          Little Endian
CPU(s):              4
On-line CPU(s) list: 0-3 # 활성화된 CPU 목록
Thread(s) per core:  1 # 각 코어당 스레드 수
Core(s) per socket:  1 # 각 소켓당 코어 수
Socket(s):           4
NUMA node(s):        1
Vendor ID:           GenuineIntel
BIOS Vendor ID:      GenuineIntel
CPU family:          6
Model:               85
Model name:          Intel(R) Xeon(R) Silver 4214 CPU @ 2.20GHz
BIOS Model name:     Intel(R) Xeon(R) Silver 4214 CPU @ 2.20GHz
Stepping:            7
CPU MHz:             2199.999
BogoMIPS:            4399.99
Hypervisor vendor:   VMware
Virtualization type: full
L1d cache:           32K
L1i cache:           32K
L2 cache:            1024K
L3 cache:            16896K
NUMA node0 CPU(s):   0-3
Flags:               fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ss syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon nopl xtopology tsc_reliable nonstop_tsc cpuid pni pclmulqdq ssse3 fma cx16 pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm abm 3dnowprefetch invpcid_single ssbd ibrs ibpb stibp ibrs_enhanced fsgsbase tsc_adjust bmi1 avx2 smep bmi2 invpcid avx512f avx512dq rdseed adx smap clflushopt clwb avx512cd avx512bw avx512vl xsaveopt xsavec xgetbv1 xsaves arat pku ospke avx512_vnni md_clear flush_l1d arch_capabilities
```



<br/>

## **2. Memory 정보 확인**

---

시스템의 메모리 모듈에 관한 정보를 출력하는 명령어 입니다.

**`# dmidecode -t memory`**

```bash
# dmidecode 3.3
Getting SMBIOS data from sysfs.
SMBIOS 2.7 present.

Handle 0x0014, DMI type 16, 23 bytes
Physical Memory Array
        Location: System Board Or Motherboard
        Use: System Memory
        Error Correction Type: None
        Maximum Capacity: 9 GB
        Error Information Handle: Not Provided
        Number Of Devices: 64

Handle 0x0015, DMI type 17, 40 bytes
Memory Device
        Array Handle: 0x0014
        Error Information Handle: No Error
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 8 GB
        Form Factor: DIMM
        Set: None
        Locator: RAM slot #0
        Bank Locator: RAM slot #0
        Type: DRAM
        Type Detail: Synchronous
        Speed: Unknown
        Manufacturer: VMware Virtual RAM
        Serial Number: 0000xxxx
        Asset Tag: Not Specified
        Part Number: VMW-8192MB
        Rank: Unknown
        Configured Memory Speed: Unknown
        Minimum Voltage: Unknown
        Maximum Voltage: Unknown
        Configured Voltage: Unknown

Handle 0x0016, DMI type 17, 40 bytes
```

<br/>

메모리 크기 정보를 확인하는 명령어입니다.

**`# dmidecode -t memory | grep -i size`**

```bash
        Size: 8 GB
        Size: No Module Installed
```

<br/>

**`free -m`**

```bash
             total        used        free      shared  buff/cache   available
Mem:           7696        2876        1837          81        2982        4423
Swap:          8083           0        8083
```

{: .prompt-info }

> **total**: 전체 메모리 또는 스왑 영역의 용량
>
> **used**: 사용 중인 메모리 또는 스왑 영역의 용량
>
> **free**: 사용 가능한(사용되지 않은) 메모리 또는 스왑 영역의 용량
>
> **shared**: 공유 메모리 영역
>
> **buff/cache**: 버퍼 및 캐시된 메모리
>
> **available**: 현재 프로세스가 할당할 수 있는 추정 가능한 메모리 양

<br/>

**`free -h`**

**`-h 옵션`**을 사용하면 읽기 쉽게 <span style='background-color:#fff5b1'>바이트(KB), 메가바이트(MB), 기가바이트(GB)</span> 등의 단위로 표시됩니다.

```bash
              total        used        free      shared  buff/cache   available
Mem:          7.5Gi       2.8Gi       1.8Gi        81Mi       2.9Gi       4.3Gi
Swap:         7.9Gi          0B       7.9Gi
```

<br/>

## **3. Disk 정보 확인**

---

**`df -h`**

디스크 파일 시스템의 사용 현황을 표시하는 명령어 입니다.

각 파일 시스템의 전체 용량, 사용된 용량, 남은 용량, 그리고 사용된 용량의 비율을 보여줍니다.

```bash

Filesystem             Size  Used Avail Use% Mounted on
devtmpfs               3.8G     0  3.8G   0% /dev
tmpfs                  3.8G   33M  3.8G   1% /dev/shm
tmpfs                  3.8G   26M  3.8G   1% /run
tmpfs                  3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/rhel-root   86G   17G   70G  19% /
/dev/sr0                12G   12G     0 100% /media
/dev/sda2             1014M  264M  751M  26% /boot
/dev/mapper/rhel-home  5.0G   83M  5.0G   2% /home
/dev/sda1              599M  5.8M  594M   1% /boot/efi
tmpfs                  770M     0  770M   0% /run/user/0
tmpfs                  770M   36K  770M   1% /run/user/1001
```

{: .prompt-info }
>**Filesystem**: 파일 시스템의 장치 또는 이름
>
>**Size**: 파일 시스템의 전체 크기
>
>**Used**: 사용된 용량
>
>**Avail**: 사용 가능한 용량
>
>**Use%**: 사용된 용량의 비율
>
>**Mounted on**: 파일 시스템이 마운트된 경로

<br/>

**`df -hT`**

파일 시스템 유형까지 보고 싶을 때 **`-T 옵션`**을 사용하여 입력하면 됩니다.

```bash
Filesystem            Type      Size  Used Avail Use% Mounted on
devtmpfs              devtmpfs  3.8G     0  3.8G   0% /dev
tmpfs                 tmpfs     3.8G   33M  3.8G   1% /dev/shm
tmpfs                 tmpfs     3.8G   26M  3.8G   1% /run
tmpfs                 tmpfs     3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/rhel-root xfs        86G   17G   70G  19% /
/dev/sr0              iso9660    12G   12G     0 100% /media
/dev/sda2             xfs      1014M  264M  751M  26% /boot
/dev/mapper/rhel-home xfs       5.0G   83M  5.0G   2% /home
/dev/sda1             vfat      599M  5.8M  594M   1% /boot/efi
tmpfs                 tmpfs     770M     0  770M   0% /run/user/0
tmpfs                 tmpfs     770M   36K  770M   1% /run/user/1001
```

<br/>

**`lsblk`**

시스템에 연결된 각 블록 장치의 계층 구조와 특성을 살펴볼 수 있는 명령어입니다.

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

{: .prompt-info }

> **NAME**: 장치 이름
>
> **MAJ**: 주/부 장치 번호
>
> **RM**: 제거 가능 여부 (1: 제거 가능, 0: 제거 불가능)
>
> **SIZE**: 장치의 전체 크기
>
> **RO**: 읽기 전용 여부 (1: 읽기 전용, 0: 읽기/쓰기 가능)
>
> **TYPE**: 장치 타입 (disk, part 등)
>
> **MOUNTPOINT**: 장치가 마운트된 포인트



<br/>

**`lsblk -f`**

각 블록 장치의 파일 시스템 유형과 UUID 등의 추가 정보를 볼 수 있는 명령어입니다.

```bash
NAME          FSTYPE      LABEL                    UUID                                   MOUNTPOINT
sda
├─sda1        vfat                                 1775-A151                              /boot/efi
├─sda2        xfs                                  0a5f6cc4-da1b-xxxx-b8c7-xxxxxxxxxxxx   /boot
└─sda3        LVM2_member                          K2KIsx-5aN4-xxxx-owBU-ahYE-xxxx-xxxxxx
  ├─rhel-root xfs                                  f1824a5a-6146-xxxx-87cc-xxxxxxxxxxxx   /
  ├─rhel-swap swap                                 fb6d246a-xxxx-44ac-aac2-xxxxxxxxxxxx   [SWAP]
  └─rhel-home xfs                                  f85df3f7-f816-4dc6-8446-xxxxxxxxxxxx   /home
sdb           LVM2_member                          mJuje5-Gvyy-KtYy-xxxx-wk2H-xxxx-xxxxxx
sr0           iso9660     RHEL-8-8-0-BaseOS-x86_64 2023-04-11-02-04-44-00                 /media
```

<br/>



**` tree /dev/disk`**

위 명령어를 사용하면 `/dev/disk` 디렉터리 아래의 디스크 관련 파일 시스템 구조를 시각적으로 확인할 수 있습니다.

```bash
/dev/disk
├── by-id
│   ├── ata-VMware_Virtual_SATA_CDRW_Drive_00000000000000000001 -> ../../sr0
│   ├── dm-name-rhel-home -> ../../dm-2
│   ├── dm-name-rhel-root -> ../../dm-0
│   ├── dm-name-rhel-swap -> ../../dm-1
│   ├── dm-uuid-LVM-ULCkkZFNVWC2s3YGL2g9vgU4gMCOYdIH27wucQVPDdu5xxxxxxxxxxxxxxxxxxxx -> ../../dm-2
│   ├── dm-uuid-LVM-ULCkkZFNVWC2s3YGL2g9vgU4gMCOYdIHl3QkhXgcAcAvxxxxxxxxxxxxxxxxxxxx -> ../../dm-1
│   ├── dm-uuid-LVM-ULCkkZFNVWC2s3YGL2g9vgU4gMCOYdIHXwv0sOMREWmuxxxxxxxxxxxxxxxxxxxx -> ../../dm-0
│   ├── lvm-pv-uuid-K2KIsx-xxxx-BSXV-xxxx-xxxx-OM35-xxxxxx -> ../../sda3
│   ├── lvm-pv-uuid-mJuje5-Gvyy-xxxx-aqt2-xxxx-xxxx-xxxxxx -> ../../sdb
│   ├── scsi-1LIO-ORG_iscsi_fileio:670b329d-xxxx-xxxx-8058-xxxxxxxxxxxx -> ../../sdb
│   ├── scsi-36001405670b3xxxxxxxxxxxxxxxxxxxx -> ../../sdb
│   ├── scsi-SLIO-ORG_iscsi_fileio_xxxx329d-xxxx-4ca3-xxxx-4719f1e2xxxx -> ../../sdb
│   └── wwn-0x6001405670b329d193a4ca380584719f -> ../../sdb
├── by-label
│   └── RHEL-8-8-0-BaseOS-x86_64 -> ../../sr0
├── by-partlabel
│   └── EFI\x20System\x20Partition -> ../../sda1
├── by-partuuid
│   ├── b2b6d6e1-xxxx-xxxx-xxxx-a3e1d5c3xxxx -> ../../sda1
│   ├── bc877837-xxxx-xxxx-xxxx-e8d2fbfexxxx -> ../../sda3
│   └── f869d840-xxxx-xxxx-xxxx-404adeb9xxxx -> ../../sda2
├── by-path
│   ├── ip-172.16.0.147:3260-iscsi-iqn.2024-05.com.gtplus.cs3:8800-lun-0 -> ../../sdb
│   ├── pci-0000:02:00.0-ata-1 -> ../../sr0
│   ├── pci-0000:03:00.0-scsi-0:0:0:0 -> ../../sda
│   ├── pci-0000:03:00.0-scsi-0:0:0:0-part1 -> ../../sda1
│   ├── pci-0000:03:00.0-scsi-0:0:0:0-part2 -> ../../sda2
│   └── pci-0000:03:00.0-scsi-0:0:0:0-part3 -> ../../sda3
└── by-uuid
    ├── 0a5f6cc4-da1b-4db9-b8c7-da9f146f6ecc -> ../../sda2
    ├── 1775-A151 -> ../../sda1
    ├── 2023-04-11-02-04-44-00 -> ../../sr0
    ├── f1824a5a-xxxx-410c-xxxx-40d5xxxxxxxx -> ../../dm-0
    ├── f85df3f7-f816-xxxx-8446-c1acxxxxxxxx -> ../../dm-2
6 directories, 30 files
```

{: .prompt-info }

> 각 서브디렉터리는 디스크 장치 및 파티션에 대한 심볼릭 링크를 포함하고 있으며, 각각의 서브디렉터리는 특정 기준에 따라 디스크를 나타냅니다.
>
> **by-id**: 디스크 장치의 제조업체와 모델을 기준으로 심볼릭 링크를 제공
>
> **by-label**: 파일 시스템 레이블을 기준으로 심볼릭 링크를 제공
>
> **by-partlabel**: 파티션 레이블을 기준으로 심볼릭 링크를 제공
>
> **by-partuuid**: 각 파티션의 UUID를 기준으로 심볼릭 링크를 제공
>
> **by-path**: 디스크가 연결된 경로를 기준으로 심볼릭 링크를 제공
>
> **by-uuid**: 파일 시스템의 UUID를 기준으로 심볼릭 링크를 제공

<br/>

## **4. LVM 정보 확인**

---

**`pvs`**

 LVM (Logical Volume Manager) 물리 볼륨에 대한 정보를 표시합니다.

```bash
  PV         VG     Fmt  Attr PSize    PFree
  /dev/sda3  rootvg lvm2 a--  <135.89g   8.00m
  /dev/sdb   datavg lvm2 a--  <200.00g <30.00g
```

{: .prompt-info }

> **PV**: 물리 볼륨의 이름 (예: /dev/sda1)
>
> **VG**: 물리 볼륨이 속한 볼륨 그룹 (Volume Group)의 이름
>
> **Fmt**: 물리 볼륨의 포맷 (예: lvm2)
>
> **Attr**: 물리 볼륨의 속성 (예: a--는 활성 상태)
>
> **PSize**: 물리 볼륨의 전체 크기
>
> **PFree**: 물리 볼륨에서 사용되지 않은 (여유) 공간의 크기

<br/>

**`vgs`**

 LVM (Logical Volume Manager) **볼륨 그룹**에 대한 정보를 표시합니다.

```bash
  VG     #PV #LV #SN Attr   VSize    VFree
  datavg   1   3   0 wz--n- <200.00g <30.00g
  rootvg   1   9   0 wz--n- <135.89g   8.00m
```

{: .prompt-info }

> **VG**: 볼륨 그룹의 이름
>
> **#PV**: 볼륨 그룹에 포함된 물리 볼륨의 수
>
> **#LV**: 볼륨 그룹에 포함된 논리 볼륨의 수
>
> **#SN**: 볼륨 그룹에 포함된 스냅샷의 수
>
> **Attr**: 볼륨 그룹의 속성
>
> **VSize**: 볼륨 그룹의 전체 크기
>
> **VFree**: 볼륨 그룹에서 사용되지 않은 (여유) 공간의 크기

<br/>



**`lvs`**

 LVM (Logical Volume Manager) 논리 볼륨에 대한 정보를 표시합니다.

```bash
  LV                     VG     Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  lv_app                 datavg -wi-ao----  50.00g
  lv_web                 datavg -wi-ao----  20.00g
  lv_root                rootvg -wi-ao----  70.00g
  ..skip..
```

{: .prompt-info }

> **LV**: 논리 볼륨의 이름
>
> **VG**: 논리 볼륨이 속한 볼륨 그룹 (Volume Group)의 이름
>
> **Attr**: 논리 볼륨의 속성
>
> **LSize**: 논리 볼륨의 크기

<br/>

{: .prompt-tip }

> `Attr` 필드 속성 의미:
>
> - **w**: 쓰기 가능 여부
> - **i**: 초기화된 볼륨
> - **a**: 활성화된 볼륨
> - **o**: 읽기 전용 볼륨
> - **p**: 영구 예약된 볼륨

<br/>

마지막으로 **`pvdisplay`, `vgdisplay`, `lvdisplay`** 명령어를 통해 물리 볼륨, 볼륨 그룹, 논리볼륨에 대한 자세한 정보를 볼 수 있습니다.



<br/>