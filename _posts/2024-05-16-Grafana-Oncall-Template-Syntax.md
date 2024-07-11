---
title: "[Grafana/OnCall] 그라파나 온콜 Email Template Syntax (이중 for문)"
date: 2024-05-16 09:00:00 +0900
categories: [APM,Grafana]
tags: [APM, Grafana, Enterprise, OnCall]
typora-root-url: ./
---

## **Grafana OnCall 템플릿 이중 for문 문법**

---

템플릿에서 사용하는 언어는 jinja2이며, 다음과 같이 템플릿에 설정하면 이중 포문을 불러올 수 있다.

```jinja2
{% raw %}
{% for num in payload.alerts %}
  -- {{loop.index}}번째 for문 --
  {% for k, v in payload.alerts[loop.index-1].labels.items() %}
    {{ loop.index }}번 = {{ k }} : {{ v -}}
  {% endfor %}
{% endfor %}
{% endraw %}
```



사진의 왼쪽을 보면 이벤트가 Payload의 alerts 객체에 하위 value들로 저장되는 구조이므로, 중앙에 작성한 문법 형식으로 작성 시, 오른쪽 텍스트로 잘 출력되는 것을 볼 수 있다.

![image-20240711150651010](/../assets/img/posts/2024-05-16-Grafana-Oncall-Template-Syntax/image-20240711150651010.png)



<br/>

## **References**

---

<https://grafana.com/docs/oncall/latest/configure/jinja2-templating/>



<br/>
