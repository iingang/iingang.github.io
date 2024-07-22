---
title: "[Linux 리눅스] 리눅스 Kickstart ISO 파일 생성(BIOS/UEFI 부팅, Baremetal(USB)/VM(CD-ROM) 설정 차이점 비교) - RHEL 8.x"
date: 2024-06-27 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, Kickstart, RedHat Linux, RHEL]
typora-root-url: ./
---



## **Environments**

---

{: .prompt-info }

> Red Hat Enterprise Linux 8.4
>
> VMware vSphere

<br/>

## **Kickstart 설치 가이드**

---

### **RHEL 8.4 dvd.iso 파일 마운트**

---

vSphere 콘솔에서 CD/DVD 드라이브에 iso 파일이 연결된 것을 확인하고 mount 명령어를 실행한다.

```bash
[root@localhost ~]# mount /dev/sr0 /media
mount: /media: WARNING: device write-protected, mounted read-only.
```

```bash
[root@localhost media]# ls -rtl
합계 48
-r--r--r--. 1 root root   103  7월  1  2022 media.repo
-r--r--r--. 1 root root  5134  7월  1  2022 RPM-GPG-KEY-redhat-release
-r--r--r--. 1 root root  1669  7월  1  2022 RPM-GPG-KEY-redhat-beta
-r--r--r--. 1 root root 18092  7월  1  2022 GPL
-r--r--r--. 1 root root  8154  7월  1  2022 EULA
-r--r--r--. 1 root root  1455  7월  1  2022 extra_files.json
dr-xr-xr-x. 2 root root  2048  7월  1  2022 isolinux
dr-xr-xr-x. 3 root root  2048  7월  1  2022 images
-r--r--r--. 1 root root  1796  7월  1  2022 TRANS.TBL
dr-xr-xr-x. 3 root root  2048  7월  1  2022 EFI
dr-xr-xr-x. 4 root root  2048  7월  1  2022 BaseOS
dr-xr-xr-x. 4 root root  2048  7월  1  2022 AppStream
```



<br/>

### **ks_rhel84 디렉토리 생성**

---

킥스타트 파일을 만들어줄 위치에 폴더를 생성한다.

```bash
mkdir -p /tmp/ks_rhel84
```



<br/>

### **디렉토리 전체 내용 복사**

---

/media로 마운트된 이미지 파일 내용 전체를 킥스타트를 생성하려는 위치인 /tmp/ks_rhel84로 전체 복사한다.

```bash
cp -avp /media/. /tmp/ks_rhel84
```

```bash
[root@localhost ks_rhel84]# ls -rtla
합계 64
-r--r--r--.  1 root root   103  7월  1  2022 media.repo
-r--r--r--.  1 root root    60  7월  1  2022 .discinfo
-r--r--r--.  1 root root  5134  7월  1  2022 RPM-GPG-KEY-redhat-release
-r--r--r--.  1 root root  1669  7월  1  2022 RPM-GPG-KEY-redhat-beta
-r--r--r--.  1 root root 18092  7월  1  2022 GPL
-r--r--r--.  1 root root  8154  7월  1  2022 EULA
-r--r--r--.  1 root root  1455  7월  1  2022 extra_files.json
-r--r--r--.  1 root root  1560  7월  1  2022 .treeinfo
dr-xr-xr-x.  2 root root   256  7월  1  2022 isolinux
dr-xr-xr-x.  3 root root    76  7월  1  2022 images
-r--r--r--.  1 root root  1796  7월  1  2022 TRANS.TBL
dr-xr-xr-x.  3 root root    18  7월  1  2022 EFI
dr-xr-xr-x.  4 root root    38  7월  1  2022 BaseOS
dr-xr-xr-x.  4 root root    38  7월  1  2022 AppStream
dr-xr-xr-x.  7 root root   259  7월  1  2022 .
drwxrwxrwt. 10 root root  4096  6월 27 16:35 ..
```

**`.discinfo`**, **`.treeinfo`** 파일까지 복사되었는지 확인하자.

<br/>

### **디렉토리 추가 생성**

---

내부 디렉토리 구성은 원하는대로 하면 되는데, 여기서는 kickstart 경로에 ks.cfg 파일을 구성하고, Patch 경로에 커널 업데이트를 위한 패키지 저장을 하려고 다음과 같이 구성했다.

```bash
mkdir -p /tmp/ks_rhel84/kickstart # Kickstart file 저장을 위한 디렉토리
mkdir -p /tmp/ks_rhel84/Patch # 패치 패키지 저장을 위한 디렉토리
```

<br/>

### **디렉토리 권한 추가**

---

복사한 파일 전체에 user 쓰기 권한을 추가한다.

```bash
chmod -R u+w /tmp/ks_rhel84/.
```

<br/>

### **패키지 파일 복사 (SFTP 사용)**

---

미리 다운로드 받은 패키지 파일들을 SFTP Tool을 이용하여 전체 복사한다.

 ```bash
 /tmp/ks_rhel/Patch 경로로 rpm 패키지 파일들 복사
 ```

<br/>



### **Repository 생성**

---

각 패치 디렉토리 위치에서 createrepo 명령어를 실행한다. (yum 설치 필요)

```bash
cd /tmp/ks_rhel84/Patch
```

```bash
createrepo .
```

```bash
[root@localhost Patch]# ls -rtl
...
-rw-r--r--. 1 root root      3096  7월  3 15:16 repomd.xml
-rw-r--r--. 1 root root  10520280  7월  3 16:14 kernel-4.18.0-513.9.1.el8_9.x86_64.rpm
drwxr-xr-x. 2 root root      4096  7월  3 16:14 repodata
```

repodata 디렉토리가 생성된 것을 볼 수 있다.



<br/>



### **킥스타트 설정 파일 생성**

---

```bash
cd /tmp/ks_rhel84
```

```bash
mkdir kickstart && cd kickstart
```

아래 링크와 /root/ anaconda-ks.cfg를 참고하여 **ks.cfg 파일을 작성하여 위 경로에 생성**한다.

1. <https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/performing_an_advanced_rhel_8_installation/creating-kickstart-files_installing-rhel-as-an-experienced-user>
2. <https://access.redhat.com/solutions/60959>

<br/>



<span style='background-color:#fff5b1'>**repo 구성, yum 패키지 설치, 디렉토리 권한, systemd 서비스 활성화 등**</span> 원하는 설정대로 /root/anaconda-ks.cfg 파일을 기반으로 작성하면 된다. 

작성할 때 주의할 점은, **VM 환경인지 Baremetal 환경인지에 따라 명령어 구성을 다르게** 해줘야 한다.

이제 필요한 내용을 변경/삭제/추가하여 커스터마이징 할 것이다.

```bash
cp /root/anaconda-ks.cfg /tmp/ks_rhel84/kickstart/ks.cfg
```

```bash
chmod 644 /tmp/ks_rhel84/kickstart/ks.cfg
```

<br/>

테스트한 내용은 실제 고객사 환경이기에 이곳에 적지는 않고, anaconda-ks.cfg 기본 파일로 적겠다.

```bash
#version=RHEL8
# Use graphical install
graphical

repo --name="AppStream" --baseurl=file:///run/install/sources/mount-0000-cdrom/AppStream

%packages
@^workstation-product-environment
@backup-client
@container-management
@development
@dotnet-core
@gnome-apps
@graphical-admin-tools
@headless-management
@internet-applications
@legacy-unix
@network-server
@office-suite
@remote-desktop-clients
@rpm-development-tools
@scientific
@security-tools
@smart-card
@system-tools
kexec-tools

%end

# Keyboard layouts
keyboard --xlayouts='kr'
# System language
lang ko_KR.UTF-8

# Network information
network  --bootproto=dhcp --device=ens192 --ipv6=auto --activate
network  --hostname=localhost.localdomain

# Use CDROM installation media
cdrom

# Run the Setup Agent on first boot
firstboot --enable

ignoredisk --only-use=sda
# System bootloader configuration
bootloader --append="crashkernel=auto" --location=mbr --boot-drive=sda
# Partition clearing information
clearpart --none --initlabel
# Disk partitioning information
part /boot/efi --fstype="efi" --ondisk=sda --size=600 --fsoptions="umask=0077,shortname=winnt"
part /boot --fstype="xfs" --ondisk=sda --size=1024
part pv.111 --fstype="lvmpv" --ondisk=sda --size=69760
volgroup rhel --pesize=4096 pv.111
logvol /home --fstype="xfs" --size=5120 --name=home --vgname=rhel
logvol swap --fstype="swap" --size=4056 --name=swap --vgname=rhel
logvol / --fstype="xfs" --size=60568 --name=root --vgname=rhel

# System timezone
timezone Asia/Seoul --isUtc --nontp

# Root password
..skip..

%addon com_redhat_kdump --enable --reserve-mb='auto'

%end

%anaconda
pwpolicy root --minlen=6 --minquality=1 --notstrict --nochanges --notempty
pwpolicy user --minlen=6 --minquality=1 --notstrict --nochanges --emptyok
pwpolicy luks --minlen=6 --minquality=1 --notstrict --nochanges --notempty
%end
```

{: .prompt-tip }

> **명령어 비교** (<span style="color: tomato">**VM (CD-ROM)**</span> / <span style="color: blue">**Baremetal (USB)**</span>)
>
> 1. Load 위치
>
>    - <span style="color: tomato">cdrom</span>
>    - <span style="color: blue">harddrive --partition=LABEL=RHEL-8-4-0- --dir=/</span>
>
> 2. Repository 설정
>
>    -  <span style="color: tomato">repo 파일 생성</span>
>
>      ```bash
>      cat > /etc/yum.repos.d/rhel.repo <<'EOF'
>      [RHEL84_Appstream]
>      name=RHEL84_APP
>      baseurl=file:///media/AppStream
>      enabled=1
>      gpgcheck=0
>      
>      [RHEL84_BaseOS]
>      name=RHEL84_BASE
>      baseurl=file:///media/BaseOS
>      enabled=1
>      gpgcheck=0
>      EOF
>      ```
>
>    - <span style="color: blue">url --url file:///mnt/intsall/repo/BaseOS</span>
>
>    - <span style="color: blue">repo --name="AppStream" --baseurl=file:///mnt/install/repo/AppStream</span>
>
> 3. Packages 설치
>
>    -  <span style="color: tomato">yum update -y kernel-4.18.0-513.9.1.el8_9.x86_64</span>
>
>    - <span style="color: blue">%packages에 패키지명 작성</span>
>
>      ```bash
>      %packages
>      kernel-4.18.0-513.9.1.el8_9.x86_64
>      ...packages...
>      %end
>      ```

{: .prompt-warning }

>  이 외에 필요한 내용 구성도, CD-ROM / USB 구성 별로 사용 명령어가 다른 점을 이해하고 작성해야 한다.

<br/>

### **Booting 항목 파일 수정**

---

Legacy BIOS 부팅 방식인지, UEFI 부팅 방식인지에 따라 수정해야할 파일이 다르다.

<br/>

#### **<span style='background-color:#fff5b1'>Legacy BIOS 부팅</span>**

**BIOS 부팅** 방식의 경우, **`isolinux.cfg`** 파일 수정

```bash
vi /tmp/ks_rhel84/isolinux/isolinux.cfg
```

```bash
default vesamenu.c32
timeout 600

display boot.msg

# Clear the screen when exiting the menu, instead of leaving the menu displayed.
# For vesamenu, this means the graphical background is still displayed without
# the menu itself for as long as the screen remains in graphics mode.
menu clear
menu background splash.png
menu title Red Hat Enterprise Linux 8.4
menu vshift 8
menu rows 18
menu margin 8
#menu hidden
menu helpmsgrow 15
menu tabmsgrow 13

# Border Area
menu color border * #00000000 #00000000 none

# Selected item
menu color sel 0 #ffffffff #00000000 none

# Title bar
menu color title 0 #ff7ba3d0 #00000000 none

# Press [Tab] message
menu color tabmsg 0 #ff3a6496 #00000000 none

# Unselected menu item
menu color unsel 0 #84b8ffff #00000000 none

# Selected hotkey
menu color hotsel 0 #84b8ffff #00000000 none

# Unselected hotkey
menu color hotkey 0 #ffffffff #00000000 none

# Help text
menu color help 0 #ffffffff #00000000 none

# A scrollbar of some type? Not sure.
menu color scrollbar 0 #ffffffff #ff355594 none

# Timeout msg
menu color timeout 0 #ffffffff #00000000 none
menu color timeout_msg 0 #ffffffff #00000000 none

# Command prompt text
menu color cmdmark 0 #84b8ffff #00000000 none
menu color cmdline 0 #ffffffff #00000000 none

# Do not display the actual menu unless the user presses a key. All that is displayed is a timeout message.

menu tabmsg Press Tab for full configuration options on menu items.

menu separator # insert an empty line
menu separator # insert an empty line

label linux
  menu label ^Install Red Hat Enterprise Linux 8.4 Kickstart VM
  kernel vmlinuz
  append initrd=initrd.img init.ks=cdrom:/kickstart/ks.cfg quiet

menu separator # insert an empty line

# utilities submenu
menu begin ^Troubleshooting
  menu title Troubleshooting

label rescue
  menu indent count 5
  menu label ^Rescue a Red Hat Enterprise Linux system
  text help
        If the system will not boot, this lets you access files
        and edit config files to try to get it booting again.
  endtext
  kernel vmlinuz
  append initrd=initrd.img inst.rescue quiet


label returntomain
  menu label Return to ^main menu
  menu exit

menu end
```

{: .prompt-info }

> OS 부팅 시 나오는 menu를 표시하는 파일이다.
>
> - <span style='background-color:#fff5b1'>64번째 라인</span>: **inst.ks** 지시문을 추가 하여 새 부팅 항목을 추가한다.
>
>   ex)  append initrd=initrd.img **init.ks=cdrom:/kickstart/ks.cfg quiet**
>
> - <span style='background-color:#fff5b1'>80번째 라인</span>: **append initrd=initrd.img inst.rescue quiet**로 변경한다.
>
>   - 구조변경 모드로 직접 부팅하도록 지시 및 로그 메시지 최소화(quiet)
>

{: .prompt-warning }

> *참고: **USB**를 만드는 경우 <span style='background-color:#E1F5FE'>64번째 라인</span>에서 CDROM을 대상으로 지정하는 대신 "**inst.ks=hd:LABEL=RHEL-8-4-0-KS-x86_64:/kickstart/ks.cfg quiet**"를 사용해야 한다.*



<br/>



#### **<span style='background-color:#fff5b1'>UEFI 부팅</span>**

**UEFI 부팅** 방식의 경우, **`grub.cfg`** 파일 수정

```bash
vi /tmp/ks_rhel84/EFI/BOOT/grub.cfg
```

```bash
set default="1"

function load_video {
  insmod efi_gop
  insmod efi_uga
  insmod video_bochs
  insmod video_cirrus
  insmod all_video
}

load_video
set gfxpayload=keep
insmod gzio
insmod part_gpt
insmod ext2

set timeout=60
### END /etc/grub.d/00_header ###

search --no-floppy --set=root -l 'RHEL-8-4-0-KS-x86_64'

### BEGIN /etc/grub.d/10_linux ###
menuentry '############## Warning: you enter KickStart Boot Menu #################' --class warning {
        linuxefi /PRESS_ENTER_AND_GOING_BACK_TO_BOOT_MENU ks=/dev/sda1
        initrdefi /hello
}
menuentry 'Install RHEL 8.4 Kickstart VM' --class fedora --class gnu-linux --class gnu --class os {
	linuxefi /images/pxeboot/vmlinuz inst.ks=cdrom:/kickstart/ks.cfg quiet
	initrdefi /images/pxeboot/initrd.img
}
submenu 'Troubleshooting -->' {
        menuentry 'Rescue a Red Hat Enterprise Linux system' --class fedora --class gnu-linux --class gnu --class os {
                linuxefi /images/pxeboot/vmlinuz inst.rescue quiet
                initrdefi /images/pxeboot/initrd.img
        }
}
```

{: .prompt-info }

> OS 부팅 시 나오는 menu를 표시하는 파일이다.
>
> - <span style='background-color:#fff5b1'>20번째 라인</span>: **search --no-floppy --set=root -l 'RHEL-8-4-0-KS-x86_64'** 로 변경
>
>   - ISO 파일 생성 명령어 입력 시, 'RHEL-8-4-0-KS-x86_64' 지정한 라벨 필요
>
>   - RUFUS를 이용하여 USB 부팅디스크를 만들면 라벨명이 'RHEL-8-4-0-' 로 잘릴 수 있음을 참고하자.
>
> - <span style='background-color:#fff5b1'>23~30번째 라인</span>:
>
>   ```bash
>   menuentry '############## Warning: you enter KickStart Boot Menu #################' --class warning {
>           linuxefi /PRESS_ENTER_AND_GOING_BACK_TO_BOOT_MENU ks=/dev/sda1
>           initrdefi /hello
>   }
>   menuentry 'Install RHEL 8.4 Kickstart' --class fedora --class gnu-linux --class gnu --class os {
>   	linuxefi /images/pxeboot/vmlinuz inst.ks=cdrom:/kickstart/ks.cfg quiet
>   	initrdefi /images/pxeboot/initrd.img
>   }
>   ```
>
> - <span style='background-color:#fff5b1'>33번째 라인</span>: **inst.rescue quiet**로 변경
>
>   - 구조변경 모드로 직접 부팅하도록 지시 및 로그 메시지 최소화(quiet)

{: .prompt-warning }

> *참고: **USB**를 만드는 경우 <span style='background-color:#E1F5FE'>64번째 라인</span>에서 CDROM을 대상으로 지정하는 대신 "**inst.ks=hd:LABEL=RHEL-8-4-0-KS-x86_64:/kickstart/ks.cfg quiet**"를 사용해야 한다.*

<br/>

### **ISO 파일 생성**

---

킥스타트 설정 파일 토대로 genisoimage를 활용하여 ISO 이미지를 추출할 수 있다.

```bash
yum install genisoimage
```

```bash
mkisofs -o /tmp/rhel-8.4-kickstart.iso -V "RHEL-8-4-0-KS-x86_64" -input-charset utf-8 -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -eltorito-alt-boot -e images/efiboot.img -no-emul-boot -J -joliet-long -R -T /tmp/ks_rhel84
```

위 명령어를 실행하면 `/tmp/ks_rhel84` 디렉토리에 있는 파일들을 기반으로 부팅 가능한 ISO 이미지 파일인 `/tmp/rhel-8.4-kickstart.iso`가 생성되며, 이 ISO 파일은 BIOS 및 UEFI 기반 시스템에서 모두 부팅 가능하다.

<br/>

{: .prompt-info }

> - 옵션 설명
>   - **`-o [File name]`**: 생성될 ISO 파일의 출력 경로와 파일명 지정
>   - **`-V`**: ISO 볼륨 이름 설정(위에서 지정한 라벨명)
>   - **`-input-charset utf-8`**: 입력 파일 시스템의 문자 인코딩 지정
>   - **`-b`**: 부팅 이미지 지정
>     - isolinux/isolinux.bin 파일을 사용하여 BIOS 기반 시스템에서 부팅할 수 있게 함
>   - **`-c`**: 부팅 카탈로그 파일 경로 지정
>   - **`-no-emul-boot`**: 실제 하드웨어와 같은 방식으로 부팅하도록 설정
>   - **`-boot-info-table`**: 부팅 정보 테이블을 생성하여 ISO 이미지에 포함시킴
>   - **`-boot-load-size 4`**: 부팅 이미지의 로드 크기를 4섹터로 설정
>   - **`-eltorito-alt-boot`**: 두 번째 부팅 이미지를 지정할 수 있게 하는 옵션
>   - **`-e`**: EFI 시스템 파티션 이미지 지정
>     - images/efiboot.img 파일을 사용하여 UEFI 기반 시스템에서 부팅할 수 있게 함
>   - **`-J`**: Joliet 확장 활성화로 Windows에서 긴 파일 이름 지원
>   - **`-R`**: Rock Ridge 확장을 활성화하여 유닉스 파일 속성(파일 소유권, 심볼릭 링크 등)을 지원
>   - **`-T`**: 파일 트리 정보 포함
>   - **`/tmp/ks_rhel84`**: ISO 이미지에 포함될 파일들이 있는 디렉토리 경로 지정

<br/>

```bash
 ...
 99.70% done, estimate finish Thu Jul  4 14:06:24 2024
 99.78% done, estimate finish Thu Jul  4 14:06:24 2024
 99.86% done, estimate finish Thu Jul  4 14:06:24 2024
 99.95% done, estimate finish Thu Jul  4 14:06:24 2024
Total translation table size: 2450633
Total rockridge attributes bytes: 1108291
Total directory bytes: 1675264
Path table size(bytes): 564
Max brk space used 9ef000
6003280 extents written (11725 MB)
```

이렇게 완료가 되면, 파일이 잘 생성된 것을 확인할 수 있다.

```bash
[root@localhost tmp]# ls -rtl /tmp/rhel-8.4-kickstart.iso
-rw-r--r--. 1 root root 12294717440  7월  4 14:06 /tmp/rhel-8.4-kickstart.iso
```



<br/>

## **알아둘 점**

{: .prompt-tip }

> - **BIOS, UEFI** 부팅 모드가 다른 경우는, 각각 변경해줘야 하는 파일(**isolinux.cfg**, **grub.cfg**)이 다르기 때문에 하나의 iso 파일에 2가지 모드를 동시에 설정해 줄 수 있다. (부팅모드를 모르면 2개 파일을 다 구성하여 iso 파일을 만들면된다.)
>
>   
>
> - **Baremetal(USB)**과 **VM(CD-ROM)**은 **1개의 ks.cfg 파일** 내에서 다르게 구성해야하므로, 동시에 설정을 못하고 각각 iso 파일을 따로 만들어야하며 변경해야할 요소가 더 많다.

<br/>

## **References**

---

<https://docs.redhat.com/ko/documentation/red_hat_enterprise_linux/8/html/customizing_anaconda/systems-with-bios-firmware_customizing-the-boot-menu>

<https://docs.redhat.com/ko/documentation/red_hat_enterprise_linux/8/html/customizing_anaconda/systems-with-uefi-firmware_customizing-the-boot-menu>

<https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html-single/performing_an_advanced_rhel_8_installation/index#creating-a-kickstart-file-by-performing-a-manual-installation_creating-kickstart-files>

<https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/8/html/system_design_guide/kickstart-script-file-format-reference_system-design-guide#kickstart-file-format_kickstart-script-file-format-reference>

<br/>