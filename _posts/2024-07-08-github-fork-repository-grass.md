---
title: "[Github 깃허브] Github 잔디 안 심어지는 현상 (feat. Git Blog)"
date: 2024-07-08 09:30:00 +0900
categories: [ETC, Git Blog]
tags: [Github, Blog]
typora-root-url: ./
---



# **Github 잔디 누락 현상**

---

github.io 블로그를 사용하고 있는데,  commit한 내용 들이 잔디에 반영되지 않고 있었다.

이유를 찾아보니, repository를 fork 해서 사용하고 있으면, original repository에 pull request를 보내고, 이 요청이 merge가 되어야 잔디에 반영되는 것을 알게 되었다.

결국 fork된 repository는 복사본이므로 여기서 어떠한 작업을 해도 원본에 영향을 주지 않으면 commit history에 남지 않는다. 

<br/>

## **Github 잔디밭에 fork 한 repository 반영하기**

---

잔디가 심어지지 않아도 블로그를 이용하는데 불편함은 없지만, commit history를 시각적으로 볼 수 있다는 장점이 있으므로 반영될 수 있게 해보자.

<br/>

### **1. 기존 Repository 이름 변경**

---

Github에 로그인해서 Repository 이름을 변경하였다.

**`iingang.github.io` -> `iingang.github.io_as`** 





<br/>

### **2. 새로운 Repository 생성**

---

원래 사용하던 레파지토리와 동일한 이름으로 새로운 **`iingang.github.io`** repo를 만들어줬다. 



<br/>

### **3. 기존 Repository 복사**

---

로컬에 디렉토리를 만들어 그곳에 **`bare clone`** 한다.

이 방식은 모든 데이터를 포함하지만 작업 디렉토리를 포함하지 않는 옵션이다. (`.git` 디렉토리 내용이 직접 보여진다.)

```bash
git clone --bare https://github.com/iingang/iingang.github.io_as.git
```



<br/>

### **4. 새로운 Repository에 Push**

---

이제 복사된 bare repository를 새로운 repository에 push 한다.

**`--mirror`** 옵션을 사용하면 모든 branch와 tag가 포함되어 정확히 복제된다.

```bash
cd iingang.github.io_as
git push --mirror  https://github.com/iingang/iingang.github.io.git
```



<br/>

### **5. 새로운 Local Repository 생성**

---

이제 원하는 경로에서 새롭게 구성한 repository를 로컬에 clone한다.

```bash
cd [로컬 repository 생성 경로]
git clone https://github.com/iingang/iingang.github.io.git
```





<br/>

### **6. 잔디밭 반영 완료**

---

위 단계를 완료하면, 기존 Repository의 모든 히스토리가 새로운 Repository에 복사된다.

기존에 반영이 안되었던 작업들도 한 번에 반영된 것을 볼 수 있다.

![image-20240708113517683](/../assets/img/posts/2024-07-08-github-fork-repository-grass/image-20240708113517683.png) 



<br/>
