---
title: "[Pacemaker/RHEL] Pacemaker STONITH 구성 (RHEL 8.X) "
date: 2024-07-03 18:00:00 +0900
categories: [Clustering, Pacemaker]
tags: [pacemaker, RHEL, RedHat]
typora-root-url: ./
---

# **Pacemaker STONITH 구성**

---

## **STONITH 란?**

---

**`STONITH`**는 "Shoot The Other Node In The Head"의 약자로 클러스터 환경에서 특정 노드를 강제로 종료하는 기능을 뜻한다.

{: .prompt-tip }

> **`STONITH`**는 **`fencing`** 메커니즘의 하나로, 클러스터 리소스의 안정성을 보장하는 중요한 역할을 한다. 
>
> **`fencing`**이란 장애가 발생한 노드를 격리하거나 종료하여 클러스터의 다른 노드에서 액세스를 제한하는 행위이다.

<br/>

### **Fence Device Types**

---

**Fence Agent (or Device)**는 클러스터가 오류 노드에 의해 공유 리소스에 대한 액세스를 제한하는 데 사용할 수 있는 외부 장치이다.

Red Hat 공식문서에서 가장 일반적인 펜싱의 2가지 유형을 다음으로 설명한다.

1. **<span style='background-color:#fff5b1'>Power fence agents</span>**: 이 에이전트들은 하드웨어를 통해 노드의 전원을 제어하여 문제가 있는 노드를 강제로 종료하거나 재부팅하여 안정성을 유지한다.
   - ex) IMPI(**`fence_ipmilan`**), iLO(**`fence_ilo`**), DRAC(**`fence_drac5`**), PDU(**`fence_apc`**) 등

2. **<span style='background-color:#fff5b1'>I/O fence agents</span>**: 이 에이전트들은 스토리지 접근을 제어하여 노드가 데이터에 접근하지 못하도록 차단하여 데이터 무결성을 보장한다.
   - ex) SCSI(**`fence_scsi`**), 다중 경로 장치(**`fence_mpath`**) 등

<br/>

고객사 환경이 Baremetal, VMware, Cloud 등의 환경에 따라 STONITH 구성 방법이 달라진다.

가상화(VMware) 및 클라우드(Cloud) 환경에서는 전통적인 Power fence agents나 I/O fence agents와 같은 하드웨어 기반이 아닌 소프트웨어 기반의 STONITH 솔루션을 사용한다.

**가상화** 환경에서는**`fence_vmware_rest`(VMware vCenter)**, **클라우드** 환경에서는 **`fence_aws`(AWS)**, **`fence_azure_arm`(Azure)**, **`fence_gce`(Google Cloud)** 등과 같은 STONITH로 구성한다. 

<br/>

이 외에도 STONITH 종류는 다양하며, **`pcs stonith list`** 명령어를 통해 확인할 수 있다.

```bash
fence_amt_ws - Fence agent for AMT (WS)
fence_apc - Fence agent for APC over telnet/ssh
fence_apc_snmp - Fence agent for APC, Tripplite PDU over SNMP
fence_bladecenter - Fence agent for IBM BladeCenter
fence_brocade - Fence agent for HP Brocade over telnet/ssh
fence_cisco_mds - Fence agent for Cisco MDS
fence_cisco_ucs - Fence agent for Cisco UCS
fence_compute - Fence agent for the automatic resurrection of OpenStack compute instances
fence_drac5 - Fence agent for Dell DRAC CMC/5
fence_eaton_snmp - Fence agent for Eaton over SNMP
fence_emerson - Fence agent for Emerson over SNMP
fence_eps - Fence agent for ePowerSwitch
fence_evacuate - Fence agent for the automatic resurrection of OpenStack compute instances
fence_heuristics_ping - Fence agent for ping-heuristic based fencing
fence_hpblade - Fence agent for HP BladeSystem
fence_ibmblade - Fence agent for IBM BladeCenter over SNMP
fence_idrac - Fence agent for IPMI
fence_ifmib - Fence agent for IF MIB
fence_ilo - Fence agent for HP iLO
fence_ilo2 - Fence agent for HP iLO
fence_ilo3 - Fence agent for IPMI
fence_ilo3_ssh - Fence agent for HP iLO over SSH
fence_ilo4 - Fence agent for IPMI
fence_ilo4_ssh - Fence agent for HP iLO over SSH
fence_ilo5 - Fence agent for IPMI
fence_ilo5_ssh - Fence agent for HP iLO over SSH
fence_ilo_moonshot - Fence agent for HP Moonshot iLO
fence_ilo_mp - Fence agent for HP iLO MP
fence_ilo_ssh - Fence agent for HP iLO over SSH
fence_imm - Fence agent for IPMI
fence_intelmodular - Fence agent for Intel Modular
fence_ipdu - Fence agent for iPDU over SNMP
fence_ipmilan - Fence agent for IPMI
fence_ipmilanplus - Fence agent for IPMI
fence_kdump - fencing agent for use with kdump crash recovery service
fence_mpath - Fence agent for multipath persistent reservation
fence_redfish - I/O Fencing agent for Redfish
fence_rhevm - Fence agent for RHEV-M REST API
fence_rsa - Fence agent for IBM RSA
fence_rsb - I/O Fencing agent for Fujitsu-Siemens RSB
fence_sbd - Fence agent for sbd
fence_scsi - Fence agent for SCSI persistent reservation
fence_virt - Fence agent for virtual machines
fence_vmware_rest - Fence agent for VMware REST API
fence_vmware_soap - Fence agent for VMWare over SOAP API
fence_watchdog - Dummy watchdog fence agent
fence_wti - Fence agent for WTI
fence_xvm - Fence agent for virtual machines
```

<br/>

특정 STONITH의 설명, 옵션 등을 보려면 **`pcs stonith describe [stonith_name]`** 명령어로 확인 가능하다.

<br/>

## **STONITH 생성**

---

### **Test Environment**

{: .prompt-info }

> Red Hat Enterprise Linux release 8.8 
>
> VMware vSphere

<br/>

### **1. fence_kdump 생성 (1번 노드에서만 입력)**

---

**`kdump`**는 Linux 커널이 비정상적인 상황에 직면했을 때, **시스템 상태**와 **디버깅 정보**를 캡처하여 덤프를 생성하는 중요 도구이다. 그렇게 수집한 덤프 파일을 vmcore라고 한다.

{: .prompt-warning }

> fence_kdump를 구성해주는 이유는, 클러스터 내 비정상 노드가 발생하여 노드를 fencing 시키기 전에 kdump를 수집할 시간을 벌어주기 위해서이다. 그 후에 STONITH를 추가로 구성해야한다.

<br/>

다음 명령어를 통해 kdump stonith를 생성한다.

**`pcs stonith create fence_kdump fence_kdump pcmk_reboot_action="off" pcmk_host_list="rhel88-01-hb rhel88-02-hb" pcmk_off_timeout=120 timeout=120`**

```bash
pcs stonith create \
fence_kdump \			# STONITH 에이전트 이름
fence_kdump \			# STONITH 에이전트 유형
pcmk_reboot_action="off" \	# 노드를 재부팅하지 않도록 설정
pcmk_host_list="rhel88-01-hb rhel88-02-hb" \	# STONITH 에이전트가 적용될 호스트 리스트
pcmk_off_timeout=120 \	# STONITH 동작 후 노드가 정상 상태로 돌아오지 않을 경우를 대비한 타임아웃 시간을 설정
timeout=120		# STONITH 에이전트가 동작하는 최대 시간을 설정하는 옵션
```

<br/>

설정이 잘 적용되었는지 확인한다.

**`pcs stonith config`**

```bash
Resource: fence_kdump (class=stonith type=fence_kdump)
  Attributes: fence_kdump-instance_attributes
    pcmk_host_list="rhel88-01-hb rhel88-02-hb"
    pcmk_off_timeout=120
    pcmk_reboot_action=off
    timeout=120
  Operations:
    monitor: fence_kdump-monitor-interval-60s
      interval=60s
```

<br/>

### **2. fence_vmware_rest 생성 (1번 노드에서만 입력)**

---

테스트 환경은 VMware 환경이므로 **`fence_vmware_rest`**로 설정할 것이다.

vmware stonith 생성을 위해 다음 명령어를 입력해준다.

```bash
pcs stonith create \
fence_vmware \	# STONITH 에이전트 이름
fence_vmware_rest \	# STONITH 에이전트 유형
ip=[vCenter IP] \	# vCenter 서버의 IP 주소
username=[Vcenter 사용자 ID] \	# vCenter 사용자의 ID
password=[vCenter 사용자 비밀번호] \	# vCenter 사용자의 비밀번호
ssl=1 ssl_insecure=1 \	# SSL을 사용하고, 인증서 검증을 하지 않도록 설정
pcmk_host_map="rhel88-01-hb:RHEL8801_internal;rhel88-02-hb:RHEL8802_internal" \	# 호스트 이름과 VMware 내부 이름 매핑
pcmk_delay_base="rhel88-01-hb:3s;rhel88-02-hb:5s" \	# 각 노드에 대한 기본 지연 시간
pcmk_delay_max=15 \	# 서로 fencing을 시도하는 현상 방지를 위해 delay(지연 시간) 최대값 설정
pcmk_reboot_timeout=180	 재부팅될 때까지 기다리는 최대 시간
```



<br/>

설정이 잘 적용되었는지 확인한다.

**`pcs stonith config`**

```bash
Resource: fence_kdump (class=stonith type=fence_kdump)
  Attributes: fence_kdump-instance_attributes
    pcmk_host_list="rhel88-01-hb rhel88-02-hb"
    pcmk_off_timeout=120
    pcmk_reboot_action=off
    timeout=120
  Operations:
    monitor: fence_kdump-monitor-interval-60s
      interval=60s
Resource: fence_vmware (class=stonith type=fence_vmware_rest)
  Attributes: fence_vmware-instance_attributes
    ip=[vCenter IP]
    password=[vCenter 사용자 비밀번호]
    pcmk_delay_base=rhel88-01-hb:3s;rhel88-02-hb:5s
    pcmk_delay_max=15
    pcmk_host_map=rhel88-01-hb:RHEL88_01;rhel88-02-hb:RHEL88_02
    pcmk_reboot_timeout=180
    ssl=1
    ssl_insecure=1
    username=[Vcenter 사용자 ID]
  Operations:
    monitor: fence_vmware-monitor-interval-60s
      interval=60s
```



<br/>

### **3. STONITH Level 설정 (1번 노드에서만 입력)**

---

노드에서 펜싱이 일어나기 전에 kdump를 수집해야 하므로, **`fence_kdump`** 작동 후에 **`fence_vmware_rest`**가 작동하도록 우선순위 설정을 해야한다.

- **`pcs stonith level add 1 rhel88-01-hb fence_kdump`**

- **`pcs stonith level add 1 rhel88-02-hb fence_kdump`**

- **`pcs stonith level add 2 rhel88-01-hb fence_vmware`**

- **`pcs stonith level add 2 rhel88-02-hb fence_vmware`**

<br/>

**`pcs stonith config`** 명령어 입력 시, 가장 밑에 레벨 설정이 된 것을 확인할 수 있다.

```bash
Fencing Levels:
 Target: rhel88-01-hb
   Level 1 - fence_kdump
   Level 2 - fence_vmware
 Target: rhel88-02-hb
   Level 1 - fence_kdump
   Level 2 - fence_vmware
```

<br/>

## **References**

---

<https://access.redhat.com/solutions/15575>

<https://docs.redhat.com/ko/documentation/red_hat_openstack_platform/16.2/html/high_availability_deployment_and_usage/assembly_fencing-controller-nodes_rhosp#con_supported-fencing-agents_fencing>

<https://access.redhat.com/solutions/2876971>

<br/>
