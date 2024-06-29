---
title: "[Network 네트워크] HTTP Request, Response 개념 및 동작 방식"
date: 2023-02-05 09:00:00 +0900
categories: [IT, Network]
tags: [IT, Network, HTTP, request, response]
typora-root-url: ./
---



## **HTTP 설명 & Client-Server 간 데이터 통신 방식**

---

**HTTP**는 **HyperText Transfer Protocol**의 약자이다.

**HTTP**란 하이퍼텍스트를 전송하기 위한 통신 규약으로, 쉽게 말해 클라이언트와 서버가 주고받는 데이터의 내용이나 순서를 정한 것이다.

웹 브라우저(클라이언트)가 웹 서버에 **요청**을 보내고, 서버가 **응답**을 반환하는 방식으로 동작한다. 

<br/>



동작 방식을 그림으로 살펴보면 다음과 같다.

![image-20240629225105033](/../assets/img/posts/2023-02-05-Network-HTTP/image-20240629225105033.png)

먼저 클라이언트는 **"무엇을"**, **"어떻게 해서"** 라는 내용을 담아 서버에게 요청을 보낸다.  

여기서 **"무엇을"**에 해당하는 내용이 <span style='background-color:#fff5b1'>**URI(Uniform Resource Identifier)**</span> 이고, **"어떻게 해서"**에 해당하는 내용이 **<span style='background-color:#fff5b1'>HTTP Method(메서드)</span>**이다.

{: .prompt-info }

> [메서드/URI 요청  예시]
>
> GET /index.html HTTP/1.1
>
> POST /api/images HTTP/1.1

<br/>

마찬가지로 서버는 클라이언트의 요청에 대해 **"요청 결과"**, **"추가 정보"**라는 결과를 담아 응답을 보낸다.  

**"요청 결과"**에는 상태 라인(Status Line), 즉 **<span style='background-color:#fff5b1'>상태 코드(Status Code), 상태 메시지, HTTP 버전</span>**이 포함되고, **"추가 정보"**에는 **<span style='background-color:#fff5b1'>Header(헤더)와 Body(바디)</span>**가 포함된다.

{: .prompt-info }

> [상태 라인 예시]
>
> HTTP/1.1 200 OK
>
> HTTP/1.1 404 Not Found

{: .prompt-info }

> [헤더 예시]
>
> Content-Type: text/html; charset=UTF-8
>
> Server: Apache/2.4.41 (Ubuntu)

{: .prompt-info }

> [바디 예시]
>
> HTML 문서, JSON 데이터



<br/>

전체적인 Request, Response 헤더의 예시는 아래와 같이 더욱 자세한 정보를 포함한다.

이는 개발자도구(F12) Network 탭에서 리소스를 클릭하면 볼 수 있다.

### **Request Header 예시**

```
GET /testApp/session.jsp HTTP/1.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
Cache-Control: max-age=0
Connection: keep-alive
Cookie: JSESSIONID=yC5kHtwq0id1viZgD8IZOlgIY6pakoODYX-DHzpt08graEQ83jtC!-1229248723
Host: 172.16.0.145
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/126.0.0.0 Safari/537.36
```

<br/>

### **Response Header 예시**

```
HTTP/1.1 200 OK
Date: Sat, 29 Jun 2024 13:11:04 GMT
X-Content-Type-Options: nosniff
Content-Length: 200
X-ORACLE-DMS-ECID: 00670SOgdCEFS8G6yzU^MG009BKC000008
X-ORACLE-DMS-RID: 0:1
X-XSS-Protection: 1; mode=block
Keep-Alive: timeout=5, max=99
Connection: Keep-Alive
Content-Type: text/html;charset=UTF-8
```



<br/>