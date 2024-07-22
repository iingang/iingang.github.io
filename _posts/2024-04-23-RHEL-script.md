---
title: "[Linux 리눅스] 리눅스(OS) 점검 파일 검토를 위한 Error 추출 명령어 (CentOS / RHEL)"
date: 2024-04-23 09:00:00 +0900
categories: [OS, Linux]
tags: [RedHat, Linux, RedHat Linux, RHEL, Script]
typora-root-url: ./
---

# **Linux 점검을 위한 명령어 실행**

---

고객사에서 OS 점검을 할 때 서버 대수가 많기 때문에, 스크립트를 돌려서 각 호스트별로 여러 항목에 대한 이상 유무에 따라 **`OK`**와 **`NOK`**를 출력 및 저장하고 있다.

점검하는 서버도 많고 항목도 많기 때문에 생성되는 파일 내용과 개수도 많으므로 이를 수동으로 체크하기에 시간이 많이 소요된다.

이 시간을 단축시키기 위해 **`NOK`**가 출력된 리스트만 파일로 저장할 수 있도록 명령어를 만들었다.

간단하게 설명하면, 스크립트를 돌려서 생성된 **`*.log`** 점검 파일들에 출력된 **`hostname`**, **`NOK`**가 포함된 항목을 추출하여 **result.txt** 파일로 저장한다.

```bash
for log in *.log; do
    echo "======================"
    echo "파일명: $log"
    echo "======================"
    sed -n '2p;/NOK/p' "$log"
done > result.txt
```

{: .prompt-tip }

> **2p**: 호스트명이 있는 2번째 행 추출
>
> **/NOK/p**: NOK가 포함된 행 추출



<br/>

위 명령어를 실행한 결과 화면은 다음과 같다.

 **`result.txt`** 파일에 문제가 되는 리스트만 추출된 것을 확인할 수 있다.

<img src="/../assets/img/posts/2024-04-23-RHEL-script/image-20240712145709926.png" alt="image-20240712145709926" style="zoom:80%;" />



<br/>
