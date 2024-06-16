---
title: "[Github 깃허브] Windows 환경에서 Github Blog 생성하기"
date: 2020-12-02 22:30:00 +0900
categories: [Git Blog]
tags: [Github, Blog, Jekyll]
---



# **Jekyll 이란?**

---

**`jekyll`**은 **정적 웹사이트** 생성기 입니다. 서버에 저장된 html, image, javascript 등의 파일이 그대로 전달된 정적인 웹페이지를 보게 됩니다. 따라서 매우 가볍다는 점이 장점입니다. 

<br/>

Jekyll은 Github에서 개발한 툴로, 핵심 역할은 **텍스트 변환 엔진**이라고 볼 수 있습니다.

Markdown 형식의 파일을 HTML 파일로 변환해주는 역할을 하기에, 이 문법에 익숙해지면 누구나 쉽게 이용할 수 있습니다.

Jekyll 을 설치하기 위해 Ruby Installer를 먼저 설치해주셔야 합니다.



<br/>

<br/>

# **Install Ruby**

---

루비를 설치하시려면 [링크](https://rubyinstaller.org/downloads/)를 클릭해주세요.

<br/>

Ruby Installer 설치가 완료되었다면, **Ruby Command 창**을 실행시켜주세요.

![image-20201117193133828](/assets/img/posts/ruby.png){: width="400" class="normal"}



<br/>

실행창에서 다음 명령어를 실행해주세요.

```ruby
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```



<br/>

설치 완료 후, 아래 명령어를 실행했을 때 버전이 잘 나오면 성공!

```ruby
jekyll -v
```



<br/>

<br/>

# **Jekyll Theme 다운로드**

---



[Jekyll Theme Page](https://jekyllthemes.io/free)에 가서 원하는 테마를 골라봅시다.



<br/>

<br/>

# **Repository 생성**

---



원하는 테마를 골랐으면 대상의 깃허브 페이지에가서 아래 url을 복사해옵니다.

![image-20201117193133828](/assets/img/posts/url.png){: width="400" class="normal"}

<br/>

<br/>

가져온 테마로 repository를 만들 차례!

새로운 레파지토리 생성하기를 누르고 아래 보이는 **Import a repository** 링크를 클릭하세요.

![image-20201117193133828](/assets/img/posts/repository.png)

<br/>

<br/>

위에서 **복사해온 url**을 첫 번째 **clone URL** 란에 입력해주고,

내 **Repository Name**에는**` [본인아이디].github.io`**를 입력해줍니다.

![image-20201117193133828](/assets/img/posts/create_repo.png)



<br/>

생성이 완료되었으면, **https://[본인아이디].github.io** 로 접속했을 때 페이지 뜨면 성공!

<br/>

<br/>

# **윈도우 Git 설치**

---

[Git 다운로드 링크](https://git-scm.com/download/win)에 가서 파일 다운로드 받습니다.

OS는 Windows 를 선택하여 가장 최신버전을 받아주세요.

<br/>

<br/>



# **로컬 Repository 생성 (내 깃허브와 연동)**

---



다운로드가 완료되면, Git Bash 를 실행시켜 주세요.

![image-20201203175217349](/assets/img/posts/git_bash.png)

<br/>



가장 먼저 해줘야 할 일은 사용자 등록입니다.

```ruby
git config --global user.name "깃허브네임"
git config --global user.email "깃허브이메일"
```





<br/>

등록이 완료됐으면, 내 깃허브 Repository URL 을 복사해주세요.

![image-20201203181525643](/assets/img/posts/me_url.png)



<br/>

복사 후, Repository 생성하고자 하는 위치로 가주세요.

> F 드라이브 밑에 만드려면 해당 디렉토리로 가서 진행! 
>
> ex) cd F

<br/>

 해당 위치에서 다음 명령어를 실행해주세요. 

```ruby
git clone [복사해온 URL] -b master --single-branch
```



<br/>



블로그 디렉토리를 잘 가져왔으면 성공!

<br/>

<br/>



# **로컬에서 블로그 실행하기**

---



로컬에 가져와진 repository 경로로 가봅시다.

```shell
cd [본인아이디].github.io
```



<br/>

해당 경로에서 아래 명령어를 순차적으로 실행해주세요.

```ruby
gem install tzinfo
gem install tzinfo-data
bundle install
bundle update
bundle install
```

<br/>

설치가 완료되었으면 다음 명령어를 실행해주세요.

```ruby
bundle exec jekyll s
```

<br/>





만약 tzinfo 관련하여 아래와 같은 에러가 발생한다면, Gemfile 파일 수정이 필요합니다.

```shell
Dependency Error: Yikes! It looks like you don't have tzinfo or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- tzinfo' If you run into trouble, you can find helpful resources at https://jekyllrb.com/help/!
```

<br/>

vi 편집기 모드로 Gemfile 파일을 열어줍니다.

```shell
vi Gemfile
```

<br/>

다음 내용을 추가하여 주세요.

```shell
gem "tzinfo"
gem "tzinfo-data"
```





<br/>

다시 아래 명령어로 웹사이트를 서버에 올려 실행해줍니다.

```shell
bundle exec jekyll s
```



![image-20210104164806811](/assets/img/posts/bundle.png)

<br/>

위와 같이 정상적으로 실행 되었다면, [http://127.0.0.1:4000](http://127.0.0.1:4000) 로 접속해주세요.

<br/>

![image-20210104175651055](/assets/img/posts/blog.png)

<br/>

접속해서 블로그에 반영한 내용을 커밋 전에 미리 확인할 수 있습니다.

<br/>

<br/>

# **Github에 올리기**

---



우선, 저장소 루트 위치에 있는  `_config.yml`은 Jeykll의 환경설정 파일이기에 기본적인 정보를 수정해봅시다.

정보를 입력 후, 해당 내용을 반영하기 위해 아래 명령어를 입력해주세요.

<br/>

**현재 위치 아래로 수정 코드 추가**

```ruby
git add .
```

<br/>

**추가한 코드에 대한 설명 작성**

```ruby
git commit -m "modify config file"
```

<br/>

**add, commit 한 코드 git server에 보내기**

```ruby
git push origin master
```

<br/>

