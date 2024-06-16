---
title: "[OS 운영체제] 운영체제 및 컴퓨터 시스템 기본 요소"
author: Ingyung Park
date: 2021-01-04 22:30:00 +0900
categories: [IT, Operating System]
tags: [IT, OS]
---

---
**Contents**

{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}
---

<br/>

# **운영체제 (Operating System) 란?**

---

**`운영체제`**는 하나 이상의 처리기로 구성된 하드웨어 자원을 활용하여 시스템 사용자에게 다양한 서비스를 제공해주는 프로그램이다.

컴퓨터 사용자와 하드웨어 간의 인터페이스 역할을 하며, 응용 프로그램이 편리한 작업을 할 수있게 환경을 제공해준다.





<br/>

<br/><br/>

# **컴퓨터의 4가지 기본 구성요소**

---

<br/>

## **처리기 (프로세서, Processor)**

---

컴퓨터의 동작을 제어하고 데이터를 처리한다.

처리기가 하나만 있는 경우에는 일반적으로 **`중앙처리장치(CPU)`**라고 부른다.



<br/>

<br/>

## **주기억장치 (Main Memory)**

----

데이터와 프로그램을 저장한다.

휘발성이기 때문에, 컴퓨터를 끄면 메모리 내용도 초기화된다.

주기억장치는 실기억장치(real memory) 혹은 주메모리(primary memory)라고도 불린다.

<br/>

> 종류 - ROM(Read Only Memory), RAM(Random Access Memory)

<br/>

<br/>

## **입출력 모듈 (I/O Module)**

---

컴퓨터와 외부 환경 간의 데이터 이동을 담당한다.

외부 환경은 보조기억장치, 통신장비, 그리고 단말기를 포함한 다양한 외부 장치로 구성된다.

<br/>



> ex ) secondary memory devices - 하드디스크
>
> ​	   communications equipment - 랜선
>
> ​	   terminals - 마우스, 스크린, 키보드

<br/>

<br/>

## **시스템 버스 (System Bus)**

---

처리기, 주기억장치, 그리고 입출력 모듈 간의 통신을 제공한다.

<br/>

<br/>![image-20210104235854772](/assets/img/posts/os1.png)

<br/>

<br/>

<br/>

# **레지스터 (Register)**

---

<br/>

**`레지스터`**란 CPU가 요청을 처리하는데 필요한 데이터를 일시적으로 저장하는 기억장치이다.

CPU 내부의 임시 저장장치인 레지스터를 사용해 명령을 빠르게 수행할 수 있다.

<br/>

> 연산 속도 :  레지스터 > RAM > 하드디스크



<br/>

<br/>

## **PC (Program Counter)**



**`프로그램 카운터`**

다음에 수행될 명령어의 주소를 저장하는 레지스터

<br/>

## **IR (Instruction Register)**

**`명령어 레지스터`**

현재 실행 중인 명령을 기억하는 레지스터

<br/>

## **MAR (Memory Address Register)**

**`메모리 주소 레지스터`**

메모리에서 가져올 데이터의 주소를 저장하는 레지스터

<br/>

## **MBR (Memory Buffer Register)**

**`메모리 버퍼 레지스터`**

메모리에 기록되거나 메모리로부터 읽힐 데이터를 저장하는 레지스터

<br/>

## **I/O AR (Input/Output Address Register)**

**`입출력 주소 레지스터`**

입출력 모듈의 주소를 가지고 있는 레지스터

<br/>

## **I/O BR (Input/Output Buffer Register)**

**`입출력 버퍼 레지스터`**

입출력 모듈과 처리기 간의 데이터 교환을 위해 사용되는 레지스터

<br/>

## **PSW (Prorgram Status Word)**

여러 프로그램이 실행되면서 발생하는 상태를 저장하는 레지스터

<br/>

> ex) 인터럽트 유/무 체크
>
> ​	OS 프로그램이 실행되고 있을 때 체크
>
> ​	조건 코드 체크 (0, 양수, 음수, overflow)

<br/>

## **AC (Accumulator)**

**`누산기`**

연산 결과를 일시적으로 저장하는 레지스터



<br/>