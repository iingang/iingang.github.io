---
title: "[Coherence] Coherence*Web Cache Server 동작 방식 "
author: Ingyung Park
date: 2022-05-15 18:00:00 +0900
categories: [Coherence]
tags: [coherence]
typora-root-url: ./
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **Coherence*Web Cache Server 동작 방식**

---

<br/>

> **coherence 3.7.1.22 버전 기준으로 작성됨**

<br/>

1. **Coherence Cache Server 동작 방식**

   - **Cache 서버의  session storage 저장 방식**은 **`Near Cache`** 방식이다.
   - **Near Cache**는 **Local Cache + Distributed Cache** (Primary/Backup) 사용
   - **`Local Cache`**는 MRU, MFU 알고리즘(페이징 기법)에 따라 로컬 캐시에 저장 (가장 최근에/가장 빈번하게 쓴 것)
   - **`Distributed Cache`**는 primary / backup 개념 존재
     - primary, backup을 찾는 매커니즘은 문서에 자세히 나와있지 않지만, 한쪽 서버로만 몰리지 않게 분배하여 동작할 것이며, primary와 backup은 서로 다른 서버에 우선적으로 분배될 것
     - 세션 복구 시, backup -> primary로 전환, primary를 가진 멤버가 다른 멤버상에 backup 새로 생성 (backup count 만큼)
     -  backup count의 기본값은 1
     - 일부 멤버가 죽어도 알아서 restore 해서 session data를 찾아올 수 있게 설계됨
   - 따라서 WebLogic <-> Coherence 간의 Connection 방식은 primary+backup 갯수 만큼 연결이 형성된다고 볼 수 있다.
   - ![image-20220620153205471](/../assets/img/posts/image-20220620153205471.png)
   - ![image-20220620153221501](/../assets/img/posts/image-20220620153221501.png)

   <br/>

2. **WebLogic 인스턴스 살아있고, Cache Server가 모두 Down 된 경우 미치는 영향**

   - 모든 WebLogic 인스턴스에서 localstorage=false 라면, Cache Server 전체 Down 시 500 Error 발생
     - 재기동이 잦은 웹로직 인스턴스는 localstorage=false 권장
   - localstorage=true를 쓰는 웹로직 인스턴스가 1개라도 있다면, Cache Server 역할도 하기 때문에 Cache Server 전체 Down되어도 500 에러 발생 X
   - primary, backup이 존재하는 Cache Server를 동시에 내리면 session data가 유실되기에, rolling restart 방식으로 Cache Server Restart 필요

   <br/>

3. **멤버 내의 Death Detection 인지 방법**

   - Death Detection이란 클러스터 멤버가 실패했을 때, 빠르게 죽음을 감지하는 방법
   - **모든 클러스터 멤버 간 TCP-Ring을 구성**하여 동작
     - TCP-Ring을 활용하여 heartbeat 간격(default: 1초) 내에 다른 노드의 사망을 감지함 
   - TCP-Ring 으로 묶여있는 멤버 간의 heartbeat 간격내에서 프로세스 죽음(TCP-Ring 구성요소) 및 하드웨어 죽음(IpMonitor 구성요소)을 모두 감지하여 이상있는 멤버는 클러스터에서 제거하는 방식



<br/>

<br/>

### **Reference**

---

[NearCache 설명](https://docs.oracle.com/cd/E24290_01/coh.371/e22837/cache_intro.htm#COHDG321)

[DeathDetection 설명](https://docs.oracle.com/cd/E24290_01/coh.371/e22837/cluster_setup.htm#COHDG5456)



