---
title: "[Linux 리눅스] Vim Visual Block(비주얼 블록) 사용 방법 - 여러 줄 한 번에 띄어쓰기 or 한 번에 주석 처리하기"
date: 2024-07-22 09:00:00 +0900
categories: [OS, Linux]
tags: [Linux, RedHat Linux, RHEL]
typora-root-url: ./
---



## **Visual Block 사용하기 - 여러 라인 한 번에 변경(띄어쓰기, 주석처리 등)**

---

vim으로 수정을 원하는 파일을 열고, **`Ctrl + v`**로 비주얼 블록 모드로 진입한다.

좌측 하단에 **<span style='background-color:#fff5b1'>VISUAL BLOCK</span>** 이라고 보이면 잘 진입한 것이다.

![image-20240722110826285](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722110826285.png)

<br/>

**`g,h,j,k`** 혹은 **방향키**를 이용해 수정을 원하는 라인만큼 선택한다.

![image-20240722110950017](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722110950017.png)

<br/>

이 상태에서 **`shift + i`**를 눌러 삽입모드로 전환한다.

전환되면 좌측 하단의 **<span style='background-color:#fff5b1'>INSERT</span>**를 볼 수 있다.

이 상태에서 띄어쓰기를 추가하거나, 단어를 삽입하거나 원하는 변경사항을 입력한다.

![image-20240722111024575](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722111024575.png)

</br>

**`Esc`**로 삽입 모드에서 빠져나오면, <span style='background-color:#fff5b1'>선택된 모든 줄에 추가한 내용(띄어쓰기, 단어 삽입, 주석 등)이 반영</span>된다.

![image-20240722111259289](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722111259289.png)

<br/>

![image-20240722111323884](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722111323884.png)

<br/>

![image-20240722112132095](/../assets/img/posts/2024-07-22-vim-visual-block/image-20240722112132095.png)
