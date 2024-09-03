---
title: "[AIX] 개행문자(^M) 삭제 방법"
date: 2022-04-02 09:00:00 +0900
categories: [OS, AIX]
tags: [AIX]
---


## **AIX 내 파일 개행문자 삭제하기**

---

Windows에서 작성한 파일을 SFTP의 **바이너리 모드**로 AIX에 전송하면 파일의 내용이 그대로 전송된다.

<br/>

Windows에서는 줄 바꿈을 위해 **carriage return(`\r`)**과 **line feed(`\n`)** 두 개의 문자를 사용하는데, 

이 때문에 Windows 파일의 **개행 문자(`\r\n`)**가 AIX에서 **`^M`**으로 표시된다.

<br/>



vi -b (바이너리 모드)로 보면 해당 문자를 볼 수 있는데, vi (vim) 모드로 열고 아래 명령어로 삭제해주면 된다. 

<span style="background-color:#fff5b1">(정확히는 공백으로 치환)</span>

<br/>

**^와 M을 직접 입력하는 것이 아니고**, **`Ctrl + v + m`**을 입력해주면 된다.

```bash
:%/^M//g
```



<br/>

<br/>