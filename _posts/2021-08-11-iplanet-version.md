---
title: "[iPlanet] iPlanet server header 정보 제거"
date: 2021-08-11 17:00:00 +0900
categories: [WEB,iPlanet]
tags: [iplanet,Web]
---


기본 설정은 아래와 같이 서버 정보 및 버전이 노출된다.

이는 보안에 취약할 수 있다.

```sh
[weblogic@test bin]$ telnet 192.168.56.2 8080
Trying 192.168.56.2...
Connected to 192.168.56.2.
Escape character is '^]'.



HTTP/1.1 400 Bad request
Server: Oracle-iPlanet-Web-Server/7.0date: Sat, 25 Jul 2020 03:42:12 GMT
Content-length: 147
Content-type: text/html
Connection: close
```



<br/>

## **http 헤더에서 server 정보 제거**

---

환경: iPlanet 7.0.27

![Image](https://raw.githubusercontent.com/iingang/iingang.github.io/master/assets/img/posts/Image.png)

<br/>

위와 같이 **[인스턴스명] > [일반] > [고급 검색]**에 들어가서 서버 헤더 설정을 사용자 정의된 이름으로 바꿔주자.

내용을 수정한 다음, 우측 상단의 보류중인 배포 누르고 인스턴스 재시작 해주면 끝!

<br/>

아래와 같이 서버 정보가 변경된 것을 확인할 수 있다.

```sh
[weblogic@test bin]$ telnet 192.168.56.2 8080
Trying 192.168.56.2...
Connected to 192.168.56.2.
Escape character is '^]'.



HTTP/1.1 400 Bad request
Server: https-test serverdate: Sat, 25 Jul 2020 03:42:36 GMT
Content-length: 147
Content-type: text/html
Connection: close
```



<br/>

${SERVER_HOME}/config 디렉토리에 있는 magnus.conf 파일에 다음 내용을 추가하는 방법도 있다.

(iPlanet 6점대에 적용)

**ServerString none** 

<br/>

<br/>

### **reference**

---

Oracle iPlanet Web Server 7.0 에서, 서버 및 버전 Header 정보를 변경하는 방법 (Doc ID 1539530.1)

How to Change the Server and Version Header in Oracle iPlanet Web Server 6.1 ? (Doc ID 1505684.1)

