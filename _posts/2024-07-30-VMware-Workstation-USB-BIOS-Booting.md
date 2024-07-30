---
title: "[Linux 리눅스] VMware Workstation Player에서 BIOS USB 부팅 디스크 실행하기 "
date: 2024-07-30 09:00:00 +0900
categories: [OS, Linux]
tags: [Linux, ISO, RHEL]
typora-root-url: ./
---



## **VMware Workstation Player USB 부팅 디스크 실행 방법**

---

VMware Workstation을 아래 링크에서 다운로드 하고 설치한다.

<https://softwareupdate.vmware.com/cds/vmw-desktop/player/17.5.2/23775571/windows/core/VMware-player-17.5.2-23775571.exe.tar>

<br/>

테스트 목적은 RHEL 8.4 kickstart (USB-Baremetal)가 잘 구성되었는지 확인하기 위해 VM을 생성하려고 한다. 

만약 내부 설정을 변경해야 되면 USB 부팅디스크를 직접 수정해서 반영할 수 있다. (VM-CDROM 방식은 다시 새로 iso 파일 만들어줘야 함)

<br/>

설치가 되면 관리자 권한으로 Workstation을 실행해주자.

![image-20240730142539989](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730142539989.png)

<br/>

다음과 같은 창을 볼 수 있다.

![image-20240730135427391](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730135427391.png)

<br/>

USB 부팅디스크로 VM을 생성해보자.

![image-20240730140108630](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140108630.png)



<br/>

빈 하드디스크로 생성한다.

![image-20240730140317626](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140317626-1722315798358-1.png)

<br/>

생성된 OS 버전을 입력한다.

![image-20240730140438688](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140438688.png)



<br/>

VM 이름과 가상 머신이 설치될 경로를 입력하고 다음을 누른다.

가상머신이 설치될 경로에 디스크에 여유 공간이 있는지 미리 확인하자.

![image-20240730140739284](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140739284.png)

<br/>

single file을 선택하고 다음을 누른다.

![image-20240730140818629](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140818629.png)

<br/>

하드웨어 옵션을 커스터마이징 하도록 하자.

![image-20240730140859325](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140859325.png)

<br/>

메모리와 CPU(Processors)를 각각 4 GB, 2 Core로 늘려준다.

![image-20240730140957856](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730140957856.png) 

<br/>

변경하고 Finish 한다.

![image-20240730141228436](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730141228436.png)



<br/>

생성된 VM에 우클릭하여 설정 메뉴에 들어간다.

![image-20240730141338428](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730141338428.png)

<br/>

먼저 NVMe 타입으로 생성된 하드디스크를 삭제한다.

![image-20240730150202410](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150202410.png)

<br/>

Add를 눌러 SCSI 타입으로 새로 생성한다.

![image-20240730150337367](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150337367.png)





![image-20240730150317118](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150317118.png)

<br/>

![image-20240730150418247](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150418247.png)

<br/>

![image-20240730150444701](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150444701.png)

<br/>

![image-20240730150513539](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150513539.png)

<br/>



이제 다시 Add를 눌러 USB를 하드 디스크로 추가해준다.

![image-20240730150557291](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150557291.png)

<br/>

디스크 타입 SCSI를 선택하고 다음을 누른다.

![image-20240730142028896](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730142028896.png)

<br/>

물리디스크로 사용하기를 선택하고 다음을 누른다.

![image-20240730142827494](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730142827494.png)



USB의 디스크 번호를 알아야하는데 이는 관리자 권한으로 cmd 창을 실행 한 후에 다음 명령어로 확인할 수 있다.

**`diskpart`**

**`list disk`**

용량으로 보아 USB는 디스크 2 임을 확인할 수 있다.

![image-20240730143326476](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730143326476.png)

<br/>

따라서 **PhysicalDrive2**를 선택하고 다음을 누른다.

![image-20240730143448053](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730143448053.png)

<br/>

이제 생성을 마치면 된다.

![image-20240730150632324](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150632324.png)

<br/>

USB용 하드디스크가 추가된 것을 확인하고 Advanced 버튼을 눌러 **`SCSI 0:1`**인 것을 기억하자. 

![image-20240730150822174](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150822174.png)

<br/>

OK 버튼을 눌러 나온다.

![image-20240730150850203](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730150850203.png)

<br/>

이제 VM을 시작하고 부팅할 때 하드디스크의 순서를 바꿔주기 위해 빠르게 F2를 연속으로 눌러 BIOS에 진입한다.

Boot 메뉴에 가서 순서를 바꿀 수 있다.

이 때, 마우스를 화면 밖으로 탈출시키려면 **`Ctrl+Alt`** 를 누르면 된다.

![image-20240730144723636](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730144723636.png)

<br/>

엔터를 눌러 메뉴를 펼치면 아까 확인한 **SCSI 하드디스크(0:1)**를 볼 수 있다.

**`+, -`** 버튼을 이용해 맨 위로 올려주자. (+가 안되면 shift랑 + 같이)

![image-20240730151030931](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730151030931.png)

<br/>

F10을 눌러 저장하고 나온다.

![image-20240730151111103](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730151111103.png)

<br/>

바로 재부팅을 하는데 USB 부팅디스크로 만든 Kickstart 파일이 잘 인식되었다.

그대로 설치를 진행하자.

![image-20240730145131414](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730145131414.png)

<br/>

![image-20240730151429145](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730151429145.png)

<br/>

설치가 완료되면 다시 재부팅하고 F2로 BIOS 모드 진입하여 하드디스크 실행 순서를 바꿔준다.

![image-20240730154257515](/../assets/img/posts/2024-07-30-VMware-Workstation-USB-BIOS-Booting/image-20240730154257515.png)

<br/>
