---
title: "[Github 깃허브/Jekyll] 구글 애드센스(Google AdSense) 적용 & 광고 위치 설정 (feat. Github Blog)"
date: 2024-05-08 09:30:00 +0900
categories: [ETC, Git Blog]
tags: [Github, Blog]
typora-root-url: ./
---



# **Github 블로그에 구글 애드센스 적용하기**

---

깃 블로그에 Google 애드센스를 적용하기 위해 구글 애드센스에 가입합니다.

블로그에 어느 정도 트래픽이 있어야 구글에서 승인을 해주는 것으로 알고 있습니다.

현재 **`Chirpy`**의 Jekyll 테마를 이용하고 있으며 어느 위치에 광고를 달면 좋은지 기록을 남기고자 포스팅을 작성합니다.



<br/>

## **1. ads.txt 파일 생성**

---

**`iingang.github.io/ads.txt`** 파일을 생성합니다.

이 파일을 통해 인증된 사람인지 확인하기 때문에 수익에 영향을 미치지 않도록 미리 만듭시다.

여기서 pub-xxx는 개인의 게시자 ID이며 **[계정] > [설정] > [계정 정보] > 게시자 ID**로 확인할 수 있습니다.

아래 형식으로 만들고 저장하면 됩니다.

```bash
google.com, pub-XXXXXXXXXXXXXXXX, DIRECT, f08c47fec0942fa0
```

<br/>

## **2. 애드센스 코드 확인**

---

구글 애드센스 홈페이지에 로그인해서 **[광고] > [사이트 기준]** 메뉴에 들어가면 **`코드 가져오기`**를 통해 내 게시자 ID를 포함한 스크립트를 확인할 수 있습니다.

![image-20240709195723714](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709195723714.png)

위 코드를 html 페이지에 추가하면 가장 적합한 위치에 광고가 자동으로 게재되는 방식입니다.

<br/>

저는 원하는 위치에 삽입하고 싶어 **[광고] > [광고 단위 기준]** 에서 **`코드 가져오기(< >)`**를 클릭하여 전체 코드를 복사했습니다.

![image-20240709201508028](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709201508028.png)

![image-20240709201809396](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709201809396.png)

<br/>



## **3. 소스코드 파일 추가**

---

위에서 복사한 코드를 아래 4곳에 추가했으며, 파일명과 위치는 다음과 같습니다.

주석으로 광고가 달릴 위치를 간단히 적어놓았으니 같은 테마를 쓰시는 분들은 참고하시면 될 것 같습니다.

<br/>

 **`iingang.github.io\_layouts\default.html`**

![image-20240709203142296](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709203142296.png)

![image-20240709203312687](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709203312687.png)



<br/>

**`iingang.github.io\_layouts\post.html`**

![image-20240709203218327](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709203218327.png)

![image-20240709203240946](/../assets/img/posts/2024-05-08-github-Blog-google-adsense/image-20240709203240946.png)



<br/>

## **References**

---

<https://support.google.com/adsense/answer/9190028?hl=ko&ref_topic=28893&sjid=14198057791408455672-AP>

<https://support.google.com/adsense/answer/9274019?hl=ko&ref_topic=28893&sjid=14198057791408455672-AP>





<br/>
