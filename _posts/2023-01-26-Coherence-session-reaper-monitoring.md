---
title: "[Coherence] Session Reaper Thread 모니터링 방법 "
date: 2023-01-26 09:00:00 +0900
categories: [Clustering, Coherence]
tags: [coherence]
typora-root-url: ./
---


## **Session Reaper Thread 모니터링 하기**

---

### **Session Reaper Thread 란?**

---

**`Session Reaper Thread`**는 세션을 관리하고 만료된 세션을 정리하는 역할을 하는 스레드이다.

GC와 유사한 기능으로 동작하며, GC 이후에 실제 인스턴스 Heap에서 제거된다. 

<br/>

만료된 세션이 지워지기 까지의 동작과정은 다음과 같다.

{: .prompt-info }

> Coherence 3.7.1.x 버전 옵션 기준

invalidation-interval-secs 값 기준으로 session을 체크하고, 

coherence-session-expire-seconds 조건에 맞는 session을 invalidate하며, (isValid=false)

이 후 GC 시 invalidate된 session들이 JVM의 heap에 제거된다.

<br/>

Heap Dump Attribute 값에서 session last access time이 session timeout이 훨씬 지난 시점에 덤프를 생성했음에도 isVaild=true 라면, session reaper thread가 원활히 동작하지 않았다고 볼 수 있다.

<br/>

![image-20240712102418440](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712102418440.png)

<br/>

Session Reap Duration의 수치 모니터링은, jconsole mbean 객체 및 visualVM에서 확인 할 수 있다.

<br/>

### **1. jconsole 모니터링**

---

Windows 환경에 Java를 설치하면 **`${JAVA_HOME}/bin/jconsole.exe`** 이 경로에서 실행하면 된다.

![png](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/.png)

<br/>



**`WebLogicHttpSessionManager > 2(Node ID) > testApp(어플리케이션 명) > Attributes`**에서 확인할 수 있다.

![png](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/-1720749291888-2)

<br/>

<img src="/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712102729032.png" alt="image-20240712102729032" style="zoom:80%;" />

{: .prompt-tip}

> **MBean 정리**
>
> (1) Reaper 주기 지표
>
> - NextReapCycle : 다음 reaper 주기 (date)
> - LastReapCycle : 최근 reaper 시간 (date)
>
> *위 두개는, invalidation-interval-secs 설정*
>
> <br/>
>
> (2) Reaper 성능 지표
>
> - AverageReapDuration : 평균 reap 시간 (ms)
> - MaxReapDuration : 최대 reap 시간 (ms)
> - LastReapDuration : 마지막 reap 에 걸린 시간 (ms)
>
> <br/>
>
> (3) Reaper에 의해 회수된 세션 갯수 지표
>
> - MaxReapedSessions : 최대치로 회수된 세션 수
>   - ReapedSessionsTotal과 다름
>   - NextReapCycle 시에 한번에 회수된 세션이 최대 몇 개인지 표기
> - ReapedSessionsTotal : 지금까지 회수된 세션 수 총합
> - AverageReapedSessions : 회수된 평균 세션 수
>   - MaxReapedSessions와 유사한 의미로 보여짐
>   - NextReapCycle 시에 한번에 회수된 세션이 많을 수록 평균치가 증가할 것으로 보임
>
> <br/>
>
> (4) 세션 객체에 대한 지표
>
> - SessionUpdates : 모든 세션 업데이트를 체크하는 수
> - SessionAverageLifetime : 세션 평균 활성화된 시간
>   - SessionTimeout 과 NextReapCycle 에 의해 변할 수 있음
> - SessionTimeout : xml 등 어플리케이션에 설정한 session timeout과 동일
> - OverflowAverageSize, OverflowMaxSize : 세션 속성의 평균,최대 크기(bytes), 테스트 어플리케이션은 1048576Bytes의 세션이며, MBean에 1049148 로 출력됨
> - OverflowUpdates : OverflowThreshold 크기 보다 큰 객체로 만들어진 세션이 얼마나 생성되었는지 지표
>   - 새로 만들기만 해도 증가함
>   - 즉 총 모든 세션 개수 + 새로고침하여 업데이트한 수
> - OverflowThreshold : 세션 객체가 이 임계값을 초과할 때 overflow로 간주되는 기준 크기
>   - 기본값 1024 bytes로 설정된 경우, session 객체 크기가 해당 bytes보다 크면 Overflow 로 통계치 분리 저장
>
> *Overflow : 특별히 큰 세션 객체에 대한 속성들*

<br/>

### **2. jvisualVM 모니터링**

---

마찬가지로 jmx 값을 모니터링할 수 있는 jvisualvm도 **`${JAVA_HOME}/bin/jvisualvm.exe`** 이 경로에서 실행하면 된다.

아래 링크에서 플러그인을 받아 추가해줘야 한다.

<https://central.sonatype.com/artifact/com.oracle.coherence.incubator/coherence-jvisualvm>

<br/>

![image-20240712105243150](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712105243150.png)

![image-20240712105251919](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712105251919.png)

<br/>

모니터링하기 위해 캐시서버에 기동스크립트에 jmx 관련 JVM 옵션 설정이 필요하다.

```bash
..SKIP..
JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote"
JAVA_OPTS="${JAVA_OPTS} -Djava.rmi.server.hostname=192.168.56.101"
JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote.port=8999"
JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote.ssl=false"
JAVA_OPTS="${JAVA_OPTS} -Dcom.sun.management.jmxremote.authenticate=false"
..SKIP..
```



<br/>

**`VisualVM > Add JMX Connection > [IP:PORT] > Do not require ssl connection 체크 > OK`**로 jmx 접속정보를 입력하여 연결할 수 있다.

![image-20240712110108809](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712110108809.png)

<br/>

**`Oracle Coherence > Coherence*Web > ReapDuration`** 에서 정확한 수치와 시각화된 그래프로 모니터링하면된다.

표를 우클릭하여 save data as.. 를 통해 csv 파일로 저장할 수도 있다.

![image-20240712103841676](/../assets/img/posts/2023-01-26-Coherence-session-reaper-monitoring/image-20240712103841676.png)

<br/>

또한 Monitor, Thread 탭에서 프로세스 자원(CPU, Memory 등)과 실행중인 Thread 목록을 확인할 수 있다.

<br/>

### **References**

---

<https://docs.oracle.com/cd/E24290_01/coh.371/e22620/reaper.htm#COHCW237>

<https://docs.oracle.com/cd/E24290_01/coh.371/e22620/manageapps.htm#COHCW341>

<https://docs.oracle.com/cd/E24290_01/coh.371/e22842/jmx.htm#COHMG243>

<https://docs.oracle.com/cd/E24628_01/install.121/e24215/coherence_monitor.htm#GSSOA11024>

<br/>

