---
title: "[WebLogic] 웹로직 콘솔 패스워드 복호화 "
date: 2021-12-08 10:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS]
typora-root-url: ./
---

# **웹로직 내 암호화된 내용 복호화 방법**

---



암호화된 웹로직 콘솔 패스워드 혹은 DB 패스워드를 다시 복호화 할 수 있다.  

boot.properties 파일에 콘솔 패드워드가 암호화 되어있는데, py 파일을 이용하여 복호화 할 수 있다.  

<br/>

복호화하는데 필요한 파일은 다음과 같다.

**[desc.py]**

```py
import os
import weblogic.security.internal.SerializedSystemIni
import weblogic.security.internal.encryption.ClearOrEncryptedService
def decrypt(domainHomeName, encryptedPwd):
      domainHomeAbsolutePath = os.path.abspath(domainHomeName)
      encryptionService = weblogic.security.internal.SerializedSystemIni.getEncryptionService(domainHomeAbsolutePath)
      ces = weblogic.security.internal.encryption.ClearOrEncryptedService(encryptionService)
      clear = ces.decrypt(encryptedPwd)
      print "Decrypted Password:" + clear
try:
  if len(sys.argv) == 3:
      decrypt(sys.argv[1], sys.argv[2])
  else:
      print "INVALID ARGUMENTS"
      print "Usage: java weblogic.WLST decryptPassword.py <DOMAIN_HOME> <ENCRYPTED_WORD>"
except:
      print "Unexpected error: ", sys.exc_info()[0]
dumpStack()
```

<br/>

웹로직 12cR2 버전에서 다음과 같이 명령어를 실행하였다.

```sh
${WLS_HOME}/oracle_common/common/bin/wlst.sh ~/desc.py ${DOMAIN_HOME} {AES}LKnneBV/b6sxZettMZqj7LjKhPuAQYugp6nGkFSToV4\=
```

**명령어: `wlst.sh desc.py 도메인홈경로 암호화된비밀번호`**



<br/>

그럼 아래와 같이 암호화된 패스워드의 평문을 확인할 수 있다.

```sh
Initializing WebLogic Scripting Tool (WLST) ...

Welcome to WebLogic Server Administration Scripting Shell

Type help() for help on available commands

Decrypted Password:weblogicadm1
```



<br/>
