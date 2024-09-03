---
title: "[Linux 리눅스] du, df 디스크 용량 차이 및 해결 방법"
date: 2024-08-27 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL]
typora-root-url: ./
---

# **du, df 명령어 차이점 및 디스크 용량이 다를 때 조치하기**

---

### **Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 8.6
>

<br/>

## **du, df 명령어 차이점 비교**

---

du와 df 명령어는 다음과 같은 다양한 이유 디스크 용량의 차이가 발생할 수 있습니다.



<br/>

1. **도구의 차이 (Tool Differences)**

   df와 du는 파일 시스템 통계를 수집하는 방식이 다릅니다. 

   df는 파일 시스템 자체에서 제공하는 정보를 바탕으로 용량을 계산하고, du는 실제 파일들의 크기를 합산하여 계산합니다.

   <br/>

2. **열려 있지만 삭제된 파일들 (Open but Deleted Files)**

   어떤 파일이 열려 있는 상태에서 삭제되면, 해당 파일은 df 출력에서는 여전히 사용 중인 용량으로 계산되지만, du에서는 포함되지 않습니다.

   이는 파일이 삭제되었지만 여전히 사용 중인 상태에서 발생합니다.

   <br/>

3. **마운트된 디렉토리 (Mounted Over Directories)**

   특정 디렉토리가 다른 파일 시스템으로 마운트되면, du는 이 디렉토리 아래에 있는 파일들을 포함하지 못할 수 있습니다. 

   반면 df는 마운트된 파일 시스템 전체를 포함하여 용량을 계산합니다.

   <br/>

4. **파일 시스템 상태 (Filesystem State)**

   파일 시스템의 내부 상태가 정확하지 않으면 df에서 제공하는 용량 정보가 부정확할 수 있습니다.

   이는 파일 시스템 자체의 오류나 비정상적인 상태로 인해 발생할 수 있습니다.

   <br/>

5. **보호된 파일 (Protected Files)**

   루트 권한 없이 실행되는 du는 접근 권한이 없는 파일들을 포함하지 않을 수 있습니다.

   이에 반해 df는 전체 파일 시스템의 정보를 제공하기 때문에 이러한 문제가 발생하지 않습니다.

   <br/>

6. **숨김 파일 (.hidden files)**

   du 명령어는 와일드카드(*)를 사용한 파일명 지정 시 특정 조건에서 숨김 파일을 포함하지 않을 수 있습니다.

   반면 df는 파일 시스템 전체의 용량을 계산하기 때문에 숨김 파일을 포함합니다.

   <br/>

7. **파일 시스템 기능 (Filesystem Features)**

   예를 들어, ext2, ext3, ext4 파일 시스템은 예약된 공간을 가지고 있는데, 이 공간은 df 출력에는 포함되지 않습니다. 

   또한 일부 NFS 클라이언트는 서버의 느린 업데이트로 인해 df가 약간 오래된 정보를 제공할 수 있습니다.

   <br/>

8. **벤더 특정 파일 시스템 (Vendor Specific Filesystem Implementations)**

   특정 벤더의 파일 시스템 구현에 따라 df와 du 간의 차이가 발생할 수 있습니다.

   예를 들어, NFS 클라이언트의 경우 지연된 업데이트로 인해 df와 du의 출력이 다를 수 있습니다.

<br/>

## **삭제되었지만 열려있는 파일 - 검색하기**

---

아래 2가지 방식으로 deleted 됐지만 df가 인식하고 있는 파일 들을 확인할 수 있습니다.

**`lsof | grep deleted`**

```bash
gunicorn    44328   44468 gunicorn                 1001    3u      REG               0,61      4096         30 /dev/shm/pym-56-96oty7zj (deleted)
gunicorn    44328   44468 gunicorn                 1001    7u      REG               0,61      4096         30 /dev/shm/pym-56-96oty7zj (deleted)
gunicorn    44328   44468 gunicorn                 1001   14u      REG               0,94         0  808923439 /tmp/wgunicorn-hyhhk9s8 (deleted)
```

<br/>

**`find /proc/*/fd -ls | grep '(deleted)'`**

```bash
 85183436      0 lrwx------   1 1001     root                      64 Sep  2 12:05 /proc/44051/fd/3 -> /dev/shm/pym-54-3xoh58sv\ (deleted)
 85183440      0 lrwx------   1 1001     root                      64 Sep  2 12:05 /proc/44051/fd/7 -> /dev/shm/pym-54-3xoh58sv\ (deleted)
 85183444      0 lrwx------   1 1001     root                      64 Sep  2 12:05 /proc/44051/fd/11 -> /tmp/wgunicorn-ph_sdcgk\ (deleted)
```

<br/>

## **삭제되었지만 열려있는 파일 - 제거하기**

---

다음 명령어는 특정 프로세스가 열어놓은 파일을 **<span style="background-color:#fff5b1">비우는 (truncate)</span>** 명령어로, 위에서 찾은 open된 파일 들을 빈 내용으로 덮어씌워 df 용량을 확보할 수 있습니다.

일반적으로 파일을 참조하는 프로세스에서 파일을 close 경우 자동으로 제거되지만, 만약 제거되지 않을 경우 해당 파일을 참조하는 프로세스를 재시작 하거나 아래 명령어로 강제로 truncate하여 비울 수 있습니다.

<br/>

**`echo > /proc/<pid>/fd/<fd_number>`**

{: .prompt-tip}

> - `/proc` 파일 시스템은 Linux에서 커널이 현재 실행 중인 프로세스와 시스템 상태를 나타내는 가상 파일 시스템입니다.
>
> - `<pid>`는 특정 프로세스의 ID(PID)를 의미하며, `<pid>`에 해당하는 디렉토리에는 해당 프로세스에 대한 정보가 담겨 있습니다.
>
> - `/proc/<pid>/fd/` 디렉토리는 해당 프로세스가 열고 있는 파일 디스크립터(file descriptor)를 나타내며, `<fd_number>`는 파일 디스크립터의 번호입니다. 각 파일 디스크립터는 프로세스가 열고 있는 파일이나 소켓을 가리킵니다.
> - `echo` 명령어는 기본적으로 지정한 문자열을 출력하는데, `>` 리다이렉션 연산자는 출력을 파일에 덮어쓰는 역할을 합니다. 여기서는 실제로 출력을 하지 않기 때문에 빈 문자열을 파일에 덮어씁니다.

<br/>

{: .prompt-warning}

> 명령어 실행 전 주의할 점은, 사용 중인 파일을 위 방식으로 자르게되면 일관성 없는 동작 혹은 정의되지 않은 동작을 발생시킬 수 있습니다.

<br/>

## **References**

---

<https://access.redhat.com/solutions/39664>

<https://access.redhat.com/solutions/2316>

<https://access.redhat.com/solutions/2794>

<https://www.redhat.com/sysadmin/du-vs-df>
