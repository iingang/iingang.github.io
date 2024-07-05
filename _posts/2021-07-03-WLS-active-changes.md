---
title: "[WebLogic] 웹로직 콘솔 변경 내용 활성화(active changes) 동작 안함"
date: 2021-07-03 09:00:00 +0900
categories: [WAS,WebLogic]
tags: [WebLogic,WAS,error]
---


# **WebLogic Console Active Changes Error**

---

WebLogic Console에서 변경 내용을 활성화 하는 작업이 제대로 동작하지 않는 현상이다.

<br/>

## **Symptoms**

---

변경 사항을 활성화하는 작업이 완료되는 데 시간이 초과되었고, 이 활성화되지 않은 변경 사항을 되돌리려는 시도가 실패하여 에러가 발생했다.

```bash
Timed out waiting for completion: Activate State: STATE_DISTRIBUTING Target Servers States: M1 STATE_DISTRIBUTED AdminServer STATE_DISTRIBUTED

weblogic.management.provider.EditFailedException: Can not undo unactivated changes while an activate changes operation is still in progress
```

<br/>

## **Solutions**

---

**`$DOMAIN_HOME/pending`** 디렉토리에 변경 보류 중인 **config.xml** 파일이 존재했다.

모든 WebLogic 인스턴스를 중지하고 **`pending`** 디렉토리를 삭제한 후에, AdminServer만 기동하여 변경 내용 활성화 버튼을 누르니 정상적으로 변경 사항이 배포되었다.



<br/>

