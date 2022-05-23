---
title: "[OHS] OHS Proxy 환경에서 DNS Cache로 인한 502 Error 발생"
author: Ingyung Park
date: 2022-05-10 18:00:00 +0900
categories: [WEB,OHS]
tags: [Web,OHS,Apache]
typora-root-url: ./
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **OHS Proxy DNS Cache 이슈**

---

<br/>

## **현상**

---

고객사에서 다음과 같은 구조로 사용 중이다.

**내부 WAS -> proxy server (OHS) -> 대외기관(AWS)**

대외기관 WAS의 ip 변동이 있을 때, DNS에 cache된 ip 정보가 있어, 변경된 ip를 인지하지 못하고, 기존 AS-IS ip로 호출하여 502 error가 발생하여 서비스가 안되는 현상이 발생하였다.

<br/>

아래는 OHS에서 발생하는 에러로그다.

```
[2022-05-10T12:01:03.2547+09:00] [OHS] [ERROR:32] [AH00957] [proxy] [host_id: proxy1] [host_addr: xxx.xxx.xxx.xxx] [pid: 11809] [tid: 140303998596864] [user: proxy] [VirtualHost: main] (70007)The timeout specified has expired: AH00957: HTTPS: attempt to connect to 1.23.456.78:443 (example.com) failed
[2022-05-10T12:01:03.2547+09:00] [OHS] [ERROR:32] [AH01114] [proxy_http] [client_id: xxx.xxx.xxx.xxx] [host_id: proxy1] [host_addr: 192.168.56.2] [pid: 11809] [tid: 140303998596864] [user: proxy] [VirtualHost: main] AH01114: HTTP: failed to make connection to backend: example.com

```



<br/>

<br/>

## **해결 방안**

---



OHS와 거의 동일한 Apache로 테스트하였다.

테스트의 핵심은 Apache의 재기동 없이, DNS의 변경된 ip 정보를 잘 인식하는 것이 목표이다.

<br/>

### **# Test 1**

로컬에서 테스트하기 위해 /etc/hosts 파일에 **`192.168.56.101 testap1`** 를 등록하고,

Apache의 httpd.conf 파일에서 ip가 아닌 DNS로 호출하는 방식을 이용하였다.

**httpd.conf**

```
ProxyRequests off
ProxyPass        / http://testap1:7002/ Keepalive=Off
ProxyPassReverse / http://testap1:7002/
```

**`Keepalive=Off`** 설정을 추가하면,  WEB -> WAS 로 정보를 받을때 소켓을 새로 열고 받게끔 해서 새로운 ip를 잘 인식하지 않을까하여 설정해보았다.

테스트 순서는 다음과 같다.

1. /etc/hosts 파일에 192.168.56.101 testap1 설정

2. Keepalive=Off 옵션을 추가한 후, Apache 재기동

3. http://testap1:7002/index.jsp 호출

4. 정상페이지 호출됨

5. /etc/hosts 파일에서 testap1의 ip 변경 

   > ex) testap1 192.168.56.103

6. 다시 새로고침 하였을 때, 변경된 ip를 인지하는듯 503 에러가 발생하였다가 Cache된 정보를 가져와서 다시 페이지가 정상적으로 보임

<br/>

Keepalive=Off 만 하면, 결국 DNS Cache로 인해 testap1의 기존 ip로 인식하는듯 보였다.

Keepalive=On도 마찬가지로, 변경된 ip를 전혀 인지하지 못하고 페이지가 정상적으로 보임.

차이점이라고 하면, Off는 첫 새로고침 할때는 TO-BE ip를 인지하는듯 하다가 결국 AS-IS ip로 호출됨.

**결론: DNS Cache 재사용으로 인해, 바뀐 ip가 아닌 기존 ip로 호출.**

<br/>

### **# Test 2**

이제 위의 테스트에서 disablereuse 옵션을 사용해보자.

disablereuse=Off 가 default 이다. 

On을 사용하면 DNS Cache를 재사용하지 않겠다는 뜻이다.

**httpd.conf**

```
ProxyRequests off
ProxyPass        / http://testap1:7002/ Keepalive=Off disablereuse=On
ProxyPassReverse / http://testap1:7002/
```

**`disablereuse=On`** 옵션을 추가한 후 재기동하였다.

테스트 순서는 다음과 같다.

1. /etc/hosts 파일에 192.168.56.101 testap1 설정

2. disablereuse=On 옵션을 추가한 후, Apache 재기동 

3. http://testap1:7002/index.jsp 호출

4. 정상페이지 호출됨

5. /etc/hosts 파일에서 testap1의 ip 변경 

   > ex) testap1 192.168.56.103

6. 다시 새로고침 하였을 때, 변경된 ip를 인지하여 잘못된 ip 호출로 503 에러 발생

<br/>

**결론: DNS Cache를 재사용하지 않아, 바뀐 ip로 정상적으로 호출.**

위 옵션을 사용하여, OHS 재기동 없이 DNS Cache된 문제를 해결할 수 있다.

<br/>

<br/>

## **Reference**

[https://httpd.apache.org/docs/2.4/mod/mod_proxy.html](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html)

<br/>
