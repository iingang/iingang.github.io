---
title: "[Apache] Apache gzip을 이용한 압축 전송 사용"
date: 2022-03-01 18:00:00 +0900
categories: [WEB,Apache]
tags: [Web,Apache]
typora-root-url: ./
---


# 아파치 gzip 압축 전송 사용 방법

---

<br/>

gzip을 이용하기 위해서는 deflate_module을 사용해야 한다.

${APACHE_HOME}/modules 경로에 mod_deflate.so 모듈이 없으면 설치하도록 하자.

<br/>

기존에 설치된 Apache에 다운로드 받은 아파치 설치파일로 apxs를 이용해 설치하였다.

```sh
$ ${APACHE_HOME}/bin/apxs -i -a -c ${APACHE_installFile}/httpd-2.4.41/modules/filters/mod_deflate.c
```



<br/>



설치 후 모듈 정상로드 되는지 확인

```sh
$ ./httpd -t -D DUMP_MODULES | grep deflate_module
deflate_module (shared)
```

<br/>

정상적으로 load 되었으면 **httpd.conf** 파일에 아래 내용 추가



```
LoadModule deflate_module modules/mod_deflate.so

<IfModule deflate_module>

AddType text/html .html .htm
AddType text/css  .css
AddType application/xml .xml
AddType application/javascript  .js
AddType application/x-httpd-php .php .php3 .html .htm .phtml .inc
AddType application/x-httpd-php-source .phps

AddOutputFilterByType DEFLATE text/plain text/html text/xml
AddOutputFilterByType DEFLATE application/xhtml+xml application/xml application/rss+xml
AddOutputFilterByType DEFLATE text/css application/javascript application/x-javascript
AddOutputFilterByType DEFLATE application/json
AddOutputFilterByType DEFLATE image/svg+xml
AddOutputFilterByType DEFLATE text/xml
AddOutputFilterByType DEFLATE text/php
AddOutputFilterByType DEFLATE application/x-httpd-php
AddOutputFilterByType DEFLATE application/x-httpd-fastphp

#사용할 압축레벨 값이 클수록 압축률이 증가하지만 CPU를 많이 사용
DeflateCompressionLevel 8

</ifModule>  
```



<br/>



설정하고 나서 해당 페이지를 호출해보자.

ex) http://WEB_IP:WEB_port/index.html

<br/>

F12를 눌러 개발자 도구에서 확인할 수 있다.

**[개발자도구] > [Network] > [Response Headers] > Content-Encoding: gzip** 으로 확인

<br/>

![Image](/../assets/img/posts/Image-16484549700631.png)





<br/>

압축을 쓰지 않을 경우 아래처럼 나온다.

![Image](/../assets/img/posts/Image-16484550042212.png)
