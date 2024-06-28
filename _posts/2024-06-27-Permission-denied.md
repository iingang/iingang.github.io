---
title: "[RHEL 8] hostnamectl, timedatectl 명령어 실행 시, 에러 발생"
date: 2024-06-27 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, Error, RedHat Linux, RHEL]
typora-root-url: ./
---

# **Symptoms**

---

```bash
root@test /root # hostnamectl set-hostname test2
Could not set property: The name org.freedesktop.hostname1 was not provided by any .service files
root@test /root # timedatectl
Failed to query server: The name org.freedesktop.timedate1 was not provided by any .service files
```

호스트명 변경하는 명령어 입력 시 아래와 같은 에러가 발생했다.

<span style="color: tomato">Could not set property: The name org.freedesktop.hostname1 was not provided by any .service files</span>

**/usr/share/dbus-1/system.d** 경로에 위에서 언급한 hostname, timedate 관련 conf 파일이 존재하나, 소유자는 root이고 권한은 644로 특이점은 없어보인다.

```bash
root@test /usr/share/dbus-1/system.d # ls -rtl
total 96
-rw-r--r--. 1 root root   985 Jun 22  2018 org.freedesktop.timedate1.conf
-rw-r--r--. 1 root root 18807 Jun 22  2018 org.freedesktop.systemd1.conf
-rw-r--r--. 1 root root   953 Jun 22  2018 org.freedesktop.resolve1.conf
-rw-r--r--. 1 root root  5153 Jun 22  2018 org.freedesktop.portable1.conf
-rw-r--r--. 1 root root 11055 Jun 22  2018 org.freedesktop.machine1.conf
-rw-r--r--. 1 root root 13299 Jun 22  2018 org.freedesktop.login1.conf
-rw-r--r--. 1 root root   975 Jun 22  2018 org.freedesktop.locale1.conf
-rw-r--r--. 1 root root  2565 Jun 22  2018 org.freedesktop.import1.conf
-rw-r--r--. 1 root root   985 Jun 22  2018 org.freedesktop.hostname1.conf
-rw-r--r--. 1 root root  1570 Aug 12  2018 org.freedesktop.ColorManager.conf
-rw-r--r--. 1 root root  1122 Dec  1  2020 org.freedesktop.bolt.conf
-rw-r--r--. 1 root root  1043 Jan 11  2021 org.freedesktop.fwupd.conf
-rw-r--r--. 1 root root   525 Jan 26  2021 org.freedesktop.UDisks2.conf
-rw-r--r--. 1 root root  1084 Feb  4  2021 FirewallD.conf
```



<br/>

dbus 서비스를 확인해보니 다음과 같이 Permission Denied 권한 에러가 발생했다.

```bash
root@test /usr/share/dbus-1/system.d # systemctl status dbus
● dbus.service - D-Bus System Message Bus
   Loaded: loaded (/usr/lib/systemd/system/dbus.service; static; vendor preset: disabled)
   Active: active (running) since Thu 2024-06-27 23:04:36 KST; 4s ago
     Docs: man:dbus-daemon(1)
 Main PID: 7495 (dbus-daemon)
    Tasks: 1 (limit: 21168)
   Memory: 960.0K
   CGroup: /system.slice/dbus.service
           └─7495 /usr/bin/dbus-daemon --system --address=systemd: --nofork --nopidfile --systemd-activation --syslog-only

Jun 27 23:04:36 test dbus-daemon[7495]: Cannot setup inotify for '/usr/share/dbus-1/system.d'; error 'Permission denied'
```



<br/>

# **Solutions**

---

최상위 / 위치에서 /etc, /usr 권한이 **750**으로 되어있다.

이는 Other 사용자가 접근할 수 없는 권한 설정이다.

일반적으로 root 사용자에게 권한 설정은 큰 제약이 되지 않지만, `hostnamectl`, `timedatectl`과 같은 시스템 도구는 DBus와 같은 시스템 서비스와 상호작용을 통해 동작한다.

따라서 이 과정에서 다양한 시스템 구성 요소가 필요한 파일에 접근할 수 있어야 하므로, 다른 사용자에게 아무 권한이 없는 설정은 문제가 될 수 있다.

```bash
root@test / # ls -rtl
total 15652
drwxr-xr-x.   2 root root       6 Jun 21  2021 srv
lrwxrwxrwx.   1 root root       8 Jun 21  2021 sbin -> usr/sbin
drwxr-xr-x.   2 root root       6 Jun 21  2021 opt
drwxr-xr-x.   2 root root       6 Jun 21  2021 media
lrwxrwxrwx.   1 root root       9 Jun 21  2021 lib64 -> usr/lib64
lrwxrwxrwx.   1 root root       7 Jun 21  2021 lib -> usr/lib
lrwxrwxrwx.   1 root root       7 Jun 21  2021 bin -> usr/bin
drwxr-x---.  12 root root     144 Aug 24  2023 usr
dr-xr-xr-x.   5 root root    4096 Jun 25 01:34 boot
drwxr-xr-x.   3 root root      26 Jun 25 01:38 mnt
-rw-r--r--.   1 root root 7995735 Jun 25 01:38 etc_ori_20240625013813.tar.gz
-rw-r--r--.   1 root root 7995735 Jun 25 01:38 etc_ori.tar.gz
drwxr-xr-x.  23 root root    4096 Jun 25 01:45 var
drwxr-xr-x.   4 root root      29 Jun 27 00:32 home
dr-xr-xr-x  277 root root       0 Jun 27 22:55 proc
dr-xr-xr-x   13 root root       0 Jun 27 22:56 sys
drwxr-xr-x   19 root root    3160 Jun 27 22:57 dev
drwxr-xr-x   43 root root    1200 Jun 27 22:57 run
dr-xr-x---.   3 root root     206 Jun 27 23:00 root
drwxrwxrwt.  12 root root    4096 Jun 27 23:06 tmp
drwxr-x---. 165 root root   12288 Jun 27 23:06 etc
```



<br/>

디렉토리 권한을 <span style='background-color:#fff5b1'>**750 -> 755**</span>으로 변경해주니 에러가 감쪽같이 사라졌다.

```bash
root@test /root # chmod 755 /etc
root@test /root # chmod 755 /usr
```

호스트명 변경도 잘 되고, DBus 서비스에 문제가 발생하지 않는다.