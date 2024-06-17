---
title: "[OHS] OHS Wallet 인증서 삭제 방법"
date: 2022-06-15 14:00:00 +0900
categories: [WEB,OHS]
tags: [Web,OHS,SSL]
typora-root-url: ./
---


# **OHS Wallet 인증서 삭제**

---

orapki wallet display 명령어로 certificate requests, user certificates, and trusted certificates 를 확인한 결과이다.

```
[weblogic@test bin]$ ./orapki wallet display -wallet ${WALLET_HOME} -complete
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Requested Certificates:
User Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Issuer:         CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Serial Number:  00:9D:25:21:8D:65:0D:5B:A0:95:23:24:41:EA:79:63:A5
Key Length      2048
MD5 digest:     4C:C9:34:4E:1F:8C:C6:73:7B:1D:C1:C6:0C:23:63:05
SHA digest:     F8:B0:FB:39:5D:7E:11:80:9E:C2:05:56:2C:F9:75:5D:66:61:7D:2E

Trusted Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Issuer:         CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Serial Number:  00:9D:25:21:8D:65:0D:5B:A0:95:23:24:41:EA:79:63:A5
Key Length      2048
MD5 digest:     4C:C9:34:4E:1F:8C:C6:73:7B:1D:C1:C6:0C:23:63:05
SHA digest:     F8:B0:FB:39:5D:7E:11:80:9E:C2:05:56:2C:F9:75:5D:66:61:7D:2E

```

<br/>



### **User Certificates 삭제 명령어**

----



```
[weblogic@test bin]$ ./orapki wallet remove -wallet ${WALLET_HOME} \
> -dn "CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR" \
> -user_cert \
> -auto_login_only
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```

<br/>



CN~ 등의 Subject 정보를 dn 옵션에 넣고 명령어를 입력하면

User Certificates 내용이 잘 제거된 것을 볼 수 있다.

```
[weblogic@test bin]$ ./orapki wallet display -wallet ${WALLET_HOME} -complete                             
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Requested Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Key Length:     2048

User Certificates:
Trusted Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Issuer:         CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Serial Number:  00:9D:25:21:8D:65:0D:5B:A0:95:23:24:41:EA:79:63:A5
Key Length      2048
MD5 digest:     4C:C9:34:4E:1F:8C:C6:73:7B:1D:C1:C6:0C:23:63:05
SHA digest:     F8:B0:FB:39:5D:7E:11:80:9E:C2:05:56:2C:F9:75:5D:66:61:7D:2E
```



<br/><br/>

### **Trust Certificates 삭제 명령어**

----



```
[weblogic@test bin]$ ./orapki wallet remove -wallet ${WALLET_HOME} \
> -dn "CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR" \
> -trusted_cert \
> -auto_login_only
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```



<br/>



Trusted Certificates 내용도 잘 제거된 것을 볼 수 있다.

```
[weblogic@test bin]$ ./orapki wallet display -wallet ${WALLET_HOME} -complete                             
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Requested Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Key Length:     2048

User Certificates:
Trusted Certificates:
```



<br/>

<br/>

### **Requested Certificates 삭제 명령어**

----



위의 내용을 다 지우면 Requested Certificates에 정보가 남아 아래 명령어로 지워줬다.

```
[weblogic@test bin]$ ./orapki wallet remove -wallet ${WALLET_HOME} \
> -dn "CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR" \
> -cert_req \
> -auto_login_only
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Operation is successfully completed.
```



<br/>



Trusted Certificates 내용도 잘 제거된 것을 볼 수 있다.

```
[weblogic@test bin]$ ./orapki wallet display -wallet ${WALLET_HOME} -complete                             
Oracle PKI Tool : Version 12.2.1.3.0
Copyright (c) 2004, 2017, Oracle and/or its affiliates. All rights reserved.

Requested Certificates:
Subject:        CN=test.com,OU=Technical Support Div.,O=TEST,L=Seocho,ST=Seoul,C=KR
Key Length:     2048

User Certificates:
Trusted Certificates:
```



<br/>

<br/>

## **Reference**

---

How to Remove the Requested Certificates Using ORAPKI command from the Oracle Wallet? (Doc ID 2766262.1)

How to Remove All Trusted and User Certificates from Wallet? (Doc ID 2780286.1)

<br/>
