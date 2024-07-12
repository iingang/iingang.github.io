---
title: "[Github 깃허브/Jekyll] Conflict: The following destination is shared by multiple files 경고 발생 (feat. Github Blog)"
date: 2024-07-11 09:30:00 +0900
categories: [ETC, Git Blog]
tags: [Github, Blog]
typora-root-url: ./
---



# **Github 블로그 충돌 메시지 발생**

---

깃 블로그를 **`bundle exec jekyll s`** 명령어를 통해 로컬 환경에서 포스팅하고 문제 없을 시 push 하는 방식으로 블로그를 포스팅하고 있다.



## **Symptoms**

---

포스팅 중 다음과 같은 Warning 메시지가 발생하였다.

![image-20240712142234413](/../assets/img/posts/2024-07-11-github-Blog-conflict-error/image-20240712142234413.png)

```bash
Conflict: The following destination is shared by multiple files.
          The written file may end up with unexpected contents.
          D:/5. Blog/iingang.github.io/_site/tags/blog/index.html
```



<br/>

확인해보니 **`Blog`**라는 태그를 사용하는 포스팅에서 대, 소문자를 다르게 사용하고 있었다. (Blog, blog)

![image-20240712141345198](/../assets/img/posts/2024-07-11-github-Blog-conflict-error/image-20240712141345198.png)

에러는 아니기 때문에 Push는 되지만 이를 수정하기로 한다.

<br/>

## **Solutions**

---

해결하기 위해, **동일한 이름으로 사용 중인 tag의 대소문자를 통일**하니 에러가 사라졌다.

![image-20240712142403806](/../assets/img/posts/2024-07-11-github-Blog-conflict-error/image-20240712142403806.png)

<br/>
