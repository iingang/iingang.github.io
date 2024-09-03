---
title: "[Container 컨테이너] podman 컨테이너 로그(ctr.log) Rotate 방법"
date: 2024-08-30 10:00:00 +0900
categories: [Container, Podman]
tags: [Container, Podman]
typora-root-url: ./
---



# **Podman 컨테이너 로그 순환하기**

---

### **Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 8.4
>
> podman version 3.0.2-dev
>



<br/>

## **ctr.log 설명 및 위치 확인 방법**

---

**`ctr.log`**는 podman container의 콘솔 로그이다.

고객사에서 컨테이너의 ctr.log가 큰 폭으로 증가하여 디스크 용량이 계속해서 차는 상황이라, 매일 기준 로그를 순환시키려고 한다.

default 경로에서 실행 중인 컨테이너의 ctr.log 확인이 가능하다. 

(컨테이너가 삭제되면 해당 컨테이너 경로도 삭제됨)

**`/var/lib/containers/storage/overlay-containers/[CONTAINER_ID]/userdata/ctr.log`**

<br/>

위 컨테이너 스토리지 구성은 **storage.conf**에 정의되어 있다.

{: .prompt-info}

> - root로 실행되는 컨테이너 엔진의 경우, storage.conf 파일은 /etc/containers/storage.conf에 저장
>
> - root가 아닌 Podman과 같은 도구로 실행하는 경우, $HOME/.config/containers/storage.conf에 저장

 

<br/>

## **Log Rotate 설정하기**

---

로그 로테이트 되기 전 상태는 이렇다.

**`ls -rtl`**

```bash
[root@localhost userdata]# ls -rtl
total 20
drwxr-xr-x. 2 root root     6 Sep  3 21:57 artifacts
drwxrwxrwt. 2 root root    40 Sep  3 22:07 shm
-rw-r--r--. 1 root root 10612 Sep  3 22:07 config.json
prw-r--r--. 1 root root     0 Sep  3 22:07 winsz
prw-r--r--. 1 root root     0 Sep  3 22:07 ctl
srwx------. 1 root root     0 Sep  3 22:07 attach
-rw-------. 1 root root  5218 Sep  3 22:31 ctr.log
```



<br/>

원래는 일자별 로테이트 설정인 **daily**로 해줘야 하지만, 순환이 되는지 테스트 해보기 위해 로그 파일의 size가 1k 이상인 경우에 순환할 수 있도록 조건을 변경했다.

<br/> 

**`vi /etc/logrotate.d/podmanlog`**

```bash
/var/lib/containers/storage/overlay-containers/*/userdata/ctr.log {
  rotate 7
  #daily
  size 1k
  compress
  missingok
  delaycompress
  copytruncate
}
```

{: .prompt-info}

> **`rotate 7`**: 최대 7개의 이전 로그 파일을 유지합니다. 가장 오래된 파일은 삭제됩니다.
>
> **`daily`**: 로그 파일을 매일 순환합니다.
>
> **`compress`**: 순환된 로그 파일을 압축하여 저장합니다.
>
> **`missingok`**: 로그 파일이 없으면 에러를 발생시키지 않습니다.
>
> **`delaycompress`**: 로그 파일을 순환한 후 다음 순환까지 압축을 지연시킵니다.
>
> **`copytruncate`**: 로그 파일을 복사한 후 원본 로그 파일을 잘라내어 새로운 로그 파일을 생성합니다.

<br/>

{: .prompt-tip}

> **첫 번째 순환**:
>
> - `ctr.log` → `ctr.log.1` (새로운 로그 파일 생성)
> - `ctr.log.1`은 압축되지 않음 (다음 순환 시점까지 지연)
>
> **두 번째 순환**:
>
> - `ctr.log` → `ctr.log.1` (새로운 로그 파일 생성)
> - `ctr.log.1` → `ctr.log.2` (이전 `ctr.log.1`은 압축되지 않고, 새로운 로그 파일로 순환됨)
> - `ctr.log.2`는 압축됨

<br/>

## **Log Rotate 실행 (crontab 설정 가능)**

---

로그 로테이트를 crontab에 등록해서 매 자정마다 실행하게끔 설정할 수 있다.

**`crontab -e`**

```bash
0 0 * * * /usr/sbin/logrotate /etc/logrotate.d/podmanlog
```

<br/>

이를 테스트하기 위해 수동으로 설정하면 동일한 명령어를 실행해주면 된다.

**`/usr/sbin/logrotate /etc/logrotate.d/podmanlog`**

<br/>

```bash
drwxr-xr-x. 2 root root     6 Sep  3 21:57 artifacts
-rw-------. 1 root root  5218 Sep  3 22:33 ctr.log.1
drwxrwxrwt. 2 root root    40 Sep  3 22:33 shm
-rw-r--r--. 1 root root 10612 Sep  3 22:33 config.json
prw-r--r--. 1 root root     0 Sep  3 22:33 winsz
prw-r--r--. 1 root root     0 Sep  3 22:33 ctl
srwx------. 1 root root     0 Sep  3 22:33 attach
-rw-------. 1 root root  1853 Sep  3 22:33 ctr.log

```

<br/>

위 명령어 2번 실행한 결과이다.

```bash
drwxr-xr-x. 2 root root     6 Sep  3 21:57 artifacts
-rw-------. 1 root root   937 Sep  3 22:33 ctr.log.2.gz
drwxrwxrwt. 2 root root    40 Sep  3 22:33 shm
-rw-r--r--. 1 root root 10612 Sep  3 22:33 config.json
prw-r--r--. 1 root root     0 Sep  3 22:33 winsz
prw-r--r--. 1 root root     0 Sep  3 22:33 ctl
srwx------. 1 root root     0 Sep  3 22:33 attach
-rw-------. 1 root root  1853 Sep  3 22:34 ctr.log.1
-rw-------. 1 root root  1853 Sep  3 22:47 ctr.log
```

<br/>

만약 강제로 실행하고 싶으면 **`-f`**, 실행중인 내용을 자세히 보고 싶으면 **`-d`** 옵션을 통해 가능하다.

{: .prompt-info}

> /usr/sbin/logrotate -f /etc/logrotate.d/podmanlog
>
> /usr/sbin/logrotate -d /etc/logrotate.d/podmanlog

<br/>

## **References**

---

<https://earihos.medium.com/how-to-use-custom-storage-location-in-podman-cc825648da90>

<https://access.redhat.com/solutions/5434641>

<br/>
