---
title: "[Linux 리눅스] RHEL Packages Download and Update (8.x)"
date: 2024-07-31 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, packages]
typora-root-url: ./
---

# **RHEL 8.x 패키지 다운로드 및 업데이트 방법**

---

### **Test Environment**

---

{: .prompt-info }

> Red Hat Enterprise Linux 8.4 (Ootpa)

{: .prompt-tip }

> - 동일한 Linux 버전 서버 2개 준비 (Public 환경 - 인터넷 o / Disconnted 환경 - 인터넷 x)
>   - Public 환경: 패키지 다운로드 및 준비
>   - Disconnected 환경: Public 환경에서 다운로드한 패키지를 옮겨 패키지 업데이트 진행
> - 모든 작업은 root user로 진행

<br/>

## **RedHat 패키지 다운로드**

---

아래 URL로 들어가면 Red Hat Packages를 다운로드 할 수 있다. (Subscription 계정 필요)

<https://access.redhat.com/downloads/content/package-browser>

<br/>

![image-20240801103043025](/../assets/img/posts/2024-07-31-RHEL-package-update/image-20240801103043025.png)

<br/>

버전을 선택하면 Repository Label명을 확인할 수 있고 직접 패키지 다운로드도 가능하다.

여기서는 레드햇 구독을 활성화하여 Public 환경에서 명령어를 통해 다운로드 하겠다.

![image-20240801103340462](/../assets/img/posts/2024-07-31-RHEL-package-update/image-20240801103340462.png)

<br/>

## **1. Public 환경 서버에서 진행**

---

설치된 openssl 패키지의 버전을 다음과 같이 업데이트 하는것이 목표이다.

- **`openssl-1.1.1g-15.el8_3.x86_64` -> `openssl-1.1.1k-8.el8_6.x86_64`**
- **`openssh-8.0p1-5.el8.x86_64` -> `openssh-8.0p1-19.el8_8.x86_64`**

<br/>

### **1.1. 패키지 업데이트 전 버전 확인**

---

**`rpm -qa | grep openssl`**

```bash
openssl-libs-1.1.1g-15.el8_3.x86_64
xmlsec1-openssl-1.2.25-4.el8.x86_64
openssl-pkcs11-0.4.10-2.el8.x86_64
openssl-1.1.1g-15.el8_3.x86_64
```

<br/>

**`rpm -qa | grep openssh`**

```bash
openssh-server-8.0p1-5.el8.x86_64
openssh-askpass-8.0p1-5.el8.x86_64
openssh-clients-8.0p1-5.el8.x86_64
openssh-8.0p1-5.el8.x86_64
```





<br/>

### **1.2. openssl, openssh 패키지 다운로드 사전 준비**

---

#### **Subscription 활성화**

---

패키지 다운로드르 위해 Red Hat 자격증명을 등록하여 구독을 활성화한다.

Red Hat에 가입된 이메일 계정과 패스워드를 입력하면 된다.

**`subscription-manager register`**

```bash
Registering to: subscription.rhsm.redhat.com:443/subscription
Username: [EMAIL_ADDRESS]
Password:
The system has been registered with 1ID: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
The registered system name is: rhel-test-01
```



<br/>

#### **Repository 활성화**

---

위 사이트에서 openssl을 검색했을 때 확인한 **`rhel-8-for-x86_64-baseos-eus-rpms`** 레이블명으로 repository를 활성화한다. (openssh 패키지도 검색하니 레이블명이 동일)

**`subscription-manager repos --enable rhel-8-for-x86_64-baseos-eus-rpms`**

```bash
Repository 'rhel-8-for-x86_64-baseos-eus-rpms' is enabled for this system.
```

여기서 레포지토리 이름에 **eus**가 포함된 것을 확인할 수 있는데, Extended Update Support(EUS)는 연장된 업데이트 지원으로 장기 지원을 받기위해 사용된다.

마이너버전이 짝수 버전일 때 eus를 사용할 수 있다.

<br/>

{: .prompt-tip }

> **/etc/yum.repos.d/redhat.repo** 파일에서도 **[rhel-8-for-x86_64-baseos-eus-rpms]** 이름 확인 가능

<br/>



#### **추가 패키지 설치**

---

yumdownloader와 repotrack을 사용하기 위해 yum-utils 설치가 필요하다.

createrepo는 yum 저장소를 생성하기위해 필요하므로 설치한다.

**`yum install createrepo yum-utils`**

```bash
Errors during downloading metadata for repository 'rhel-8-for-x86_64-baseos-eus-rpms':
  - Status code: 404 for https://cdn.redhat.com/content/eus/rhel8/8/x86_64/baseos/os/repodata/repomd.xml (IP: 104.76.92.83)
Error: Failed to download metadata for repo 'rhel-8-for-x86_64-baseos-eus-rpms': Cannot download repomd.xml: Cannot download repodata/repomd.xml: All mirrors were tried
```



<br/>

설치하려고하니 위와 같은 에러가 발생한다.

그 이유는 EUS repo를 사용하려면 release를 설정해줘야 하기 때문이다.

Openssl을 업데이트 버전을 보면 **`el8_6`**이므로 8.6 으로 세팅해야한다.

<br/>

설정이 가능한 release 목록은 다음과 같다.

**`subscription-manager release --list`**

```bash
+-------------------------------------------+
          Available Releases
+-------------------------------------------+
8
8.0
8.1
8.10
8.2
8.3
8.4
8.5
8.6
8.7
8.8
8.9
```

<br/>

**`subscription-manager release --set=8.6`**

```bash
Release set to: 8.6
```

<br/>

이제 설치가 잘 된다.

**`yum install createrepo yum-utils`**

```bash
Installed:
  createrepo_c-0.17.7-5.el8.x86_64   createrepo_c-libs-0.17.7-5.el8.x86_64
  drpm-0.4.1-3.el8.x86_64            yum-utils-4.0.21-11.el8.noarch

Complete!
```



<br/>

### **1.3. openssl, openssh 패키지 다운로드**

---

패키지 파일을 다운로드 받을 디렉토리를 생성한다.

**`mkdir openssl && cd openssl`**

<br/>

resolve 옵션을 걸어 의존성있는 다른 패키지들까지 다운로드 받는다. 

**`yumdownloader --resolve openssl-1.1.1k-8.el8_6.x86_64`**

```bash
(1/2): openssl-1.1.1k-8.el8_6.x86_64.rpm 1.4 MB/s | 709 kB     00:00
(2/2): openssl-libs-1.1.1k-8.el8_6.x86_64.rpm 5.7 MB/s | 1.5 MB     00:00
```

yumdownloader가 아닌 repotrack 명령어를 사용하면 더 복잡한 패키지 추적 및 다운로드가 가능하다.

<br/>

**`yumdownloader --resolve openssh-8.0p1-19.el8_8.x86_64`**

```bash
Last metadata expiration check: 0:00:10 ago on Wed 31 Jul 2024 02:37:03 PM KST.
(1/3): openssh-server-8.0p1-19.el8_8.x86_64. 1.7 MB/s | 493 kB     00:00
(2/3): openssh-clients-8.0p1-19.el8_8.x86_64 285 kB/s | 669 kB     00:02
(3/3): openssh-8.0p1-19.el8_8.x86_64.rpm     102 kB/s | 524 kB     00:05
```

yumdownloader가 아닌 repotrack 명령어를 사용하면 더 복잡한 패키지 추적 및 다운로드가 가능하다.

<br/>

**`ls -rtl`**

```bash
-rw-r--r-- 1 root root  726424 Jul 31 14:14 openssl-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:15 openssl-libs-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r-- 1 root root  726424 Jul 31 14:40 openssh-server-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:40 openssh-clients-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:40 openssh-8.0p1-19.el8_8.x86_64.rpm
```

<br/>

#### **저장소 생성**

---

해당 경로에서 rpm 패키지 저장소를 만들어준다.

**`createrepo .`**

```bash
Directory walk started
Directory walk done - 1 packages
Temporary output repo path: ./.repodata/
Preparing sqlite DBs
Pool started (with 5 workers)
Pool finished
```



<br/>

repodata 디렉토리가 생성되었다.

**`ls -rtl`**

```bash
-rw-r--r-- 1 root root  726424 Jul 31 14:14 openssl-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:15 openssl-libs-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r-- 1 root root  726424 Jul 31 14:40 openssh-server-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:40 openssh-clients-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r-- 1 root root 1538344 Jul 31 14:40 openssh-8.0p1-19.el8_8.x86_64.rpm
drwxr-xr-x 2 root root    4096 Jul 31 14:16 repodata
```

이 환경에서 진행할거면 yum cache를 지워줘야 하지만 옮겨서 설치할 것이기 때문에 옮긴 서버에서 진행하면 된다.

<br/>

#### **저장소 전송**

---

이제 만들어진 저장소를 Disconnected 환경으로 전송한다.

**`scp -i rhel86key.pem -r openssl/ root@[Disconneted 환경 IP]:/tmp`**

```bash
The authenticity of host '192.168.0.5 (192.168.0.5)' can't be established.
ECDSA key fingerprint is SHA256:..SKIP../..SKIP...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.0.5' (ECDSA) to the list of known hosts.
openssl-1.1.1k-8.el8_6.x86_64.rpm          100%  709KB 298.9MB/s   00:00
openssl-libs-1.1.1k-8.el8_6.x86_64.rpm     100% 1502KB 347.0MB/s   00:00
63b258947441fb2cd13769127fb0b54e63e395d9e0 100% 1499     2.7MB/s   00:00
7df2836836500ab705fd908601a89471ad3431d761 100% 1382     2.2MB/s   00:00
d945f69a0735d2580c38497f5618fafbc6791143b1 100% 1465     2.1MB/s   00:00
b24c5f111ff23ec214f97598f1c1df6b2486a9a8e9 100% 3955     6.0MB/s   00:00
123389f40a16a6f114a9da7158793cc3aa5814fb9b 100% 2450     4.9MB/s   00:00
2e7b452c87038d129cad99b3e459e843dd4d519221 100% 2590     3.9MB/s   00:00
repomd.xml                                 100% 3076     4.6MB/s   00:00
```

{: .prompt-info }

> SSH Key를 이용해 터미널에 접속해야할 때 i 옵션을 적용한다.

<br/>



## **2. Disconnected 환경 서버에서 진행**

---

### **2.1. 패키지 설치를 위한 사전준비**

---

#### **Local Repository 생성**

---

전달받은 rpm 패키지 저장소를 .repo 파일에 등록해준다.

```bash
cat <<EOF> /etc/yum.repos.d/local.repo
[packages]
name=openssl_openssh_update
baseurl=file:///tmp/openssl
enabled=1
gpgcheck=0
EOF
```

<br/>

#### **yum 캐시 삭제 및 repolist 확인**

---

레포지토리를 변경하면 항상 yum 캐시를 지워주자.

레포지토리가 잘 반영됐는지 목록을 확인한다.

**`yum clean all && yum repolist`**

```bash
Updating Subscription Management repositories.
repo id                                                                         repo name
packages                                                                         openssl_openssh_update
```



<br/>

### **2.2. openssl, openssh 패키지 설치**

---



#### **패키지 설치 (실패)**

---

**`yum install openssl openssh`**

```bash
Last metadata expiration check: 0:02:48 ago on Wed 31 Jul 2024 02:43:01 PM KST.
Package openssl-1:1.1.1g-15.el8_3.x86_64 is already installed.
Package openssh-8.0p1-5.el8.x86_64 is already installed.
Error:
 Problem: cannot install both openssh-8.0p1-19.el8_8.x86_64 and openssh-8.0p1-5.el8.x86_64
  - package openssh-askpass-8.0p1-5.el8.x86_64 requires openssh = 8.0p1-5.el8, but none of the providers can be installed
  - cannot install the best candidate for the job
  - problem with installed package openssh-askpass-8.0p1-5.el8.x86_64
(try to add '--allowerasing' to command line to replace conflicting packages or '--skip-broken' to skip uninstallable packages or '--nobest' to use not only best candidate packages)
```

구성한 로컬 레포지토리로 openssl, openssh를 설치하려는데 의존성 에러가 발생한다.

에러 내용을 토대로 추론하면 openssh-8.0p1-19.el8_8.x86_64 버전이 필요하니 yumdownloader로 다운로드 받아야한다.

현재 이 서버에 설치된 버전은 openssh-askpass-8.0p1-5.el8.x86_64 이다.

  

<br/>

#### **추가 패키지 다운로드 (이 단계만 Public 환경에서 진행)**

---

패키지를 Public 다운로드하고 위와 같은 방식으로 다시 Disconnected 환경으로 넘겨준다.

**`yumdownloader --resolve openssh-askpass-8.0p1-19.el8_8.x86_64`**

<br/>

**`scp -i rhel86key.pem openssh-askpass-8.0p1-19.el8_8.x86_64 root@[Disconneted 환경 IP]:/tmp/openssl`**

**/tmp/openssl** 경로로 옮겨주고 **`createrepo .`**, **`yum clean all && yum repolist`** 를 진행하여 저장소를 다시 만들어준다.



<br/>

#### **패키지 저장소 최종 확인**

---

**`ls -rtl`**

```bash
total 4008
-rw-r--r--. 1 root root  536288 Aug  1 14:42 openssh-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r--. 1 root root  685144 Aug  1 14:42 openssh-clients-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r--. 1 root root  504792 Aug  1 14:42 openssh-server-8.0p1-19.el8_8.x86_64.rpm
-rw-r--r--. 1 root root  726424 Aug  1 14:42 openssl-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r--. 1 root root 1538344 Aug  1 14:42 openssl-libs-1.1.1k-8.el8_6.x86_64.rpm
-rw-r--r--. 1 root root   96596 Aug  1 15:07 openssh-askpass-8.0p1-19.el8_8.x86_64.rpm
drwxr-xr-x. 2 root root    4096 Aug  1 15:12 repodata
```





<br/>

#### **패키지 설치 재시도 (성공)**

---

이제 잘 실행되는 것을 볼 수 있다. y 눌러 설치하면 된다. (`-y` 옵션 가능)

**`yum install openssl openssh`**

```bash
Unable to read consumer identity

This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.

openssl_openssh_update                                                                                                                                                        5.4 MB/s | 5.5 kB     00:00
Package openssl-1:1.1.1g-15.el8_3.x86_64 is already installed.
Package openssh-8.0p1-5.el8.x86_64 is already installed.
Dependencies resolved.
==============================================================================================================================================================================================================
 Package                                              Architecture                                Version                                                 Repository                                     Size
==============================================================================================================================================================================================================
Upgrading:
 openssh                                              x86_64                                      8.0p1-19.el8_8                                          packages                                      524 k
 openssh-askpass                                      x86_64                                      8.0p1-19.el8_8                                          packages                                       94 k
 openssh-clients                                      x86_64                                      8.0p1-19.el8_8                                          packages                                      669 k
 openssh-server                                       x86_64                                      8.0p1-19.el8_8                                          packages                                      493 k
 openssl                                              x86_64                                      1:1.1.1k-8.el8_6                                        packages                                      709 k
 openssl-libs                                         x86_64                                      1:1.1.1k-8.el8_6                                        packages                                      1.5 M

Transaction Summary
==============================================================================================================================================================================================================
Upgrade  6 Packages

Total size: 3.9 M
Is this ok [y/N]
```

<br/>



#### **패키지 업데이트 후 설치 버전 확인**

---

**`rpm -qa | grep openssl`**

```bash
openssl-libs-1.1.1k-8.el8_6.x86_64
openssl-pkcs11-0.4.10-2.el8.x86_64
xmlsec1-openssl-1.2.25-4.el8.x86_64
openssl-1.1.1k-8.el8_6.x86_64
```

<br/>

**`rpm -qa | grep openssh`**

```bash
openssh-clients-8.0p1-19.el8_8.x86_64
openssh-server-8.0p1-19.el8_8.x86_64
openssh-8.0p1-19.el8_8.x86_64
openssh-askpass-8.0p1-19.el8_8.x86_64
```



