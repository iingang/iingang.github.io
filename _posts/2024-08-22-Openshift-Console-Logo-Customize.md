---
title: "[OpenShift 오픈시프트] Openshift Web Console Logo 커스터마이징 방법"
date: 2024-08-22 10:30:00 +0900
categories: [Cloud,OpenShift]
tags: [Cloud, OpenShift, RedHat]
typora-root-url: ./
---



# **OCP 웹 콘솔 로고 변경하기**

---

### **Test Environment**

---

{: .prompt-info}

> Red Hat Enterprise Linux 9.2 (Plow)
>
> RHOCP 4.14.15



<br/>

## **1. Logo Image 업로드**

---

이미지 로고를 선택할 때, GIF, JPG, PNG, SVG를 포함한 일반적인 이미지 형식의 파일이 가능하며 **최대 높이 60px**로 제한되어 있다. 

또한 이미지 크기가 **1MB**를 초과해서는 안 된다.

해당 이미지를 Bastion 노드에 업로드하자.

<br/>

 **`/opt/openshift/logo`** 위치에 **custom-logo.png** 파일을 업로드했다.

<br/>

## **2. Configmap 생성**

---



openshift-install 명령어 자동완성 기능을 활성화하기 위해 다음 명령어를 실행한다.

**`oc create configmap custom-logo --from-file /opt/openshift/logo/custom-logo.png -n openshift-config`**

```bash
configmap/custom-logo created
```

<br/>

## **3.  Openshift Console Operator 수정**

---

여기서 편집 명령어를 통해 spec 하위의 customization 부분을 추가하고 저장한다.

**`customProductName`**은 브라우저 탭에 나오는 텍스트인데, 한글도 잘 나온다. (참고로 이미지 파일이 깨지면 이미지 대신 입력한 글자로 나옴) 

**`oc edit consoles.operator.openshift.io cluster`**

```bash
apiVersion: operator.openshift.io/v1
kind: Console
metadata:
  name: cluster
spec:
  customization:
    customLogoFile:
      key: custom-logo.png
      name: custom-logo
    customProductName: iinaglow 입니다
```

![image-20240826210748491](/../assets/img/posts/2024-08-22-Openshift-Console-Logo-Customize/image-20240826210748491.png)

<br/>



```bash
console.operator.openshift.io/cluster edited
```



<br/>

## **4. Logo 결과 확인**

---

**[변경 전]**

![image-20240826205743602](/../assets/img/posts/2024-08-22-Openshift-Console-Logo-Customize/image-20240826205743602.png)



![image-20240826205803266](/../assets/img/posts/2024-08-22-Openshift-Console-Logo-Customize/image-20240826205803266.png)

<br/>

**[변경 후]**

![image-20240826210441596](/../assets/img/posts/2024-08-22-Openshift-Console-Logo-Customize/image-20240826210441596.png)

![image-20240826210502380](/../assets/img/posts/2024-08-22-Openshift-Console-Logo-Customize/image-20240826210502380.png)

<br/>

## **References**

---

<https://docs.openshift.com/container-platform/4.14/web_console/customizing-the-web-console.html>

<br/>
