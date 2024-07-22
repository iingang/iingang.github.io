---
title: "[Linux 리눅스] RHEL 7,8 Local Repository 설정 (yum 사용)"
date: 2024-07-01 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, repository, yum]
typora-root-url: ./
---

## **RHEL 7 버전 Repository 설정**

---

VMware vSphere 환경에서 CD/DVD 드라이브에 RHEL 7.x dvd.iso 파일이 연결되어있는지 확인한다.

(기타 다른 VMware 환경에서도 마찬가지)

![image-20240701122341738](/../assets/img/posts/2024-07-01-RHEL-repository/image-20240701122341738.png)

<br/>

### **위치 지정하여 ISO 파일 mount** 

---

기본으로 생성되는 /media 위치에 마운트를 할 것이다.

다른 곳에 마운트 하기를 원하면 그 경로에 디렉토리를 생성해주면 된다.

```bash
[root@localhost /]# mount /dev/sr0 /media
mount: /dev/sr0 is write-protected, mounting read-only
```

<br/>

/media 경로에 가면 잘 연결된 것을 볼 수 있다.

```bash
[root@localhost media]# ls -rtl
total 974
-r--r--r--. 1 root root   3211 Jul 14  2020 RPM-GPG-KEY-redhat-release
-r--r--r--. 1 root root   3375 Jul 14  2020 RPM-GPG-KEY-redhat-beta
-r--r--r--. 1 root root  18092 Jun 28  2022 GPL
-r--r--r--. 1 root root   8266 Jun 28  2022 EULA
-r--r--r--. 1 root root   1455 Jun 28  2022 extra_files.json
-r--r--r--. 1 root root    114 Jun 28  2022 media.repo
-r--r--r--. 1 root root   1796 Jun 28  2022 TRANS.TBL
dr-xr-xr-x. 2 root root   2048 Jun 28  2022 repodata
dr-xr-xr-x. 2 root root 946176 Jun 28  2022 Packages
dr-xr-xr-x. 2 root root   2048 Jun 28  2022 LiveOS
dr-xr-xr-x. 2 root root   2048 Jun 28  2022 isolinux
dr-xr-xr-x. 3 root root   2048 Jun 28  2022 images
dr-xr-xr-x. 3 root root   2048 Jun 28  2022 EFI
dr-xr-xr-x. 4 root root   2048 Jun 28  2022 addons
```



<br/>

### **Local Repository 구성**

---

```bash
[root@localhost media]# cat > /etc/yum.repos.d/local.repo
[local-repo]
name=Local Repository
baseurl=file:///media
enabled=1
gpgcheck=0
^C
```

위 경로에 local.repo 파일을 만들고 Ctrl+c로 빠져나온다.

<br/>

repo 파일이 잘 생성되었는지 확인한다.

```bash
[root@localhost media]# cat /etc/yum.repos.d/local.repo
[local-repo]
name=Local Repository
baseurl=file:///media
enabled=1
gpgcheck=0
```



<br/>

### **Respository 확인**

---

yum clean 명령어를 사용해 cache를 정리해주고 repolist 명령어로 repository가 잘 활성화 되었는지 확인한다.

```bash
[root@localhost media]# yum clean all; yum repolist
Loaded plugins: product-id, search-disabled-repos, subscription-manager

This system is not registered with an entitlement server. You can use subscription-manager to register.

Cleaning repos: local-repo
Loaded plugins: product-id, search-disabled-repos, subscription-manager

This system is not registered with an entitlement server. You can use subscription-manager to register.

local-repo                                                                                                                                                                             | 2.8 kB  00:00:00
(1/2): local-repo/group                                                                                                                                                                | 628 kB  00:00:00
(2/2): local-repo/primary                                                                                                                                                              | 2.1 MB  00:00:00
local-repo                                                                                                                                                                                          5230/5230
repo id                                                                                          repo name                                                                                              status
local-repo                                                                                       Local Repository                                                                                       5,230
repolist: 5,230
```

<br/>

yum 명령어가 잘 동작하는 것을 볼 수 있다.

```bash
[root@localhost media]# yum install net-tools
Loaded plugins: product-id, search-disabled-repos, subscription-manager

This system is not registered with an entitlement server. You can use subscription-manager to register.

Resolving Dependencies
--> Running transaction check
---> Package net-tools.x86_64 0:2.0-0.25.20131004git.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================================================================================================================================
 Package                                       Arch                                       Version                                                        Repository                                      Size
==============================================================================================================================================================================================================
Installing:
 net-tools                                     x86_64                                     2.0-0.25.20131004git.el7                                       local-repo                                     306 k

Transaction Summary
==============================================================================================================================================================================================================
Install  1 Package

Total download size: 306 k
Installed size: 918 k
Is this ok [y/d/N]:

```



<br/>



## **RHEL 8 버전 Repository 설정**

---

VMware vSphere 환경에서 CD/DVD 드라이브에 RHEL 8.x dvd.iso 파일이 연결되어있는지 확인한다.

(기타 다른 VMware 환경에서도 마찬가지)

![image-20240701122257206](/../assets/img/posts/2024-07-01-RHEL-repository/image-20240701122257206.png)





<br/>

### **위치 지정하여 ISO 파일 mount** 

---

기본으로 생성되는 /media 위치에 마운트를 할 것이다.

다른 곳에 마운트 하기를 원하면 그 경로에 디렉토리를 생성해주면 된다.

```bash
[root@localhost /]# mount /dev/sr0 /media
mount: /dev/sr0 is write-protected, mounting read-only
```

<br/>

/media 경로에 가면 잘 연결된 것을 볼 수 있다.

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

### **Local Repository 구성**

---

```bash
[root@localhost media]# cat > /etc/yum.repos.d/local.repo
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
^C
```

위 경로에 local.repo 파일을 만들고 Ctrl+c로 빠져나온다.

<br/>

repo 파일이 잘 생성되었는지 확인한다.

```bash
[root@localhost media]# cat /etc/yum.repos.d/local.repo
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
```



<br/>

### **Respository 확인**

---

yum clean 명령어를 사용해 cache를 정리해주고 repolist 명령어로 repository가 잘 활성화 되었는지 확인한다.

```bash
[root@localhost media]# yum clean all; yum repolist
Updating Subscription Management repositories.
Unable to read consumer identity

This system is not registered with an entitlement server. You can use subscription-manager to register.

...

repo id                                                                                                repo name
AppStream                                                                                              AppStream
BaseOS                                                                                                 BaseOS

```

<br/>

