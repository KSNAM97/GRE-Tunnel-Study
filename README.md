![Topic](https://img.shields.io/badge/Topic-GRE_Tunnel-blue)
![Feature](https://img.shields.io/badge/Feature-Point--to--Point%20%7C%20Tunnel_Routing-orange)
![Vendor](https://img.shields.io/badge/Vendor-Cisco-1BA0D7?logo=cisco&logoColor=white)
![Tool](https://img.shields.io/badge/Tool-GNS3-2ECC71)
![Level](https://img.shields.io/badge/Level-Advanced-red)


# 🌐 GRE Tunnel Study (Static / Dynamic / MGRE)

> Cisco 라우터를 활용한 **GRE Tunnel** 종합 실습 저장소입니다.  
> Frame-Relay 백본 위에 OSPF / EIGRP로 공중망(Public Network)을 구성하고,  
> 본사 ↔ 지사 간을 **GRE Tunnel (Static / Dynamic)** 및 **MGRE + NHRP**로 연결하며,  
> NAT Overload(PAT) 및 EIGRP / OSPF 기반 사설망 라우팅까지 단계별로 학습합니다.

---

## 📑 목차 (Table of Contents)

- [1. 토폴로지 (Topology)](#1-토폴로지-topology)
- [2. 장비 구성 (Device Role)](#2-장비-구성-device-role)
- [3. 기본 구성 (Basic Configuration)](#3-기본-구성-basic-configuration)
- [4. Chapter 1. 공중망 구성 (OSPF)](#4-chapter-1-공중망-구성-ospf)
- [5. Chapter 2. NAT Overload (PAT)](#5-chapter-2-nat-overload-pat)
- [6. Chapter 3. GRE Tunnel (Static) — 물리 IP 기반](#6-chapter-3-gre-tunnel-static--물리-ip-기반)
- [7. Chapter 4. GRE Tunnel (Dynamic) + EIGRP — Loopback 기반](#7-chapter-4-gre-tunnel-dynamic--eigrp--loopback-기반)
- [8. Chapter 5. MGRE + NHRP](#8-chapter-5-mgre--nhrp)
- [9. 패킷 캡슐화 구조 (Packet Encapsulation)](#9-패킷-캡슐화-구조-packet-encapsulation)
- [10. 검증 명령어 (Verification)](#10-검증-명령어-verification)

---

## 1. 토폴로지 (Topology)

### GRE Tunnel (Static/Dynamic) Topology

![GRE Tunnel Topology](/topology/GRE_Topology.png)

### MGRE Topology

![MGRE Topology](/topology/MGRE_Topology.png)

---

## 2. 장비 구성 (Device Role)

### Chapter 1 ~ 4 (GRE Static/Dynamic)

| 장비 | Hostname | 역할 | 주요 IP |
|------|----------|------|---------|
| R1 | SOL-A (GIT-A) | 본사 Gateway | s1/0.10: 121.160.10.1 / Lo0: 100.100.1.1 / Lo1: 100.100.10.1 |
| R2 | SOL-B (GIT-B) | 지사 Gateway | s1/0.20: 121.160.20.2 / Lo0: 100.100.2.2 / Lo1: 100.100.20.2 |
| R3 | ISP-1 | 공중망 | 121.160.10.11 / 121.160.12.1 / Lo0: 100.100.11.1 |
| R4 | ISP-2 | 공중망 | 121.160.12.2 / 121.160.23.2 / Lo120: 120.20.2.2 |
| R5 | ISP-3 | 공중망 | 121.160.23.3 / 121.160.34.3 / Lo130: 130.30.3.3 |
| R6 | ISP-4 | 공중망 | 121.160.34.4 / 121.160.20.4 / Lo0: 100.100.14.4 |
| R7 | PC1 | SOL-A 내부 PC | 192.168.10.1 |
| R8 | PC2 | SOL-B 내부 PC | 192.168.20.2 |

### Chapter 5 (MGRE)

| 장비 | Hostname | 역할 | 주요 IP |
|------|----------|------|---------|
| R1 | ISP-1 | 공중망 (HQ측) | F0/0: 211.241.14.11 / Lo0: 110.110.1.1 / Lo1: 110.110.10.1 |
| R2 | ISP-2 | 공중망 (A측) | F0/0: 211.241.25.22 / Lo0: 110.110.2.2 / Lo1: 110.110.20.2 |
| R3 | ISP-3 | 공중망 (B측) | F0/0: 211.241.36.33 / Lo0: 110.110.3.3 / Lo1: 110.110.30.3 |
| R4 | GIT-HQ | 서울 본사 (**NHRP Server / Hub**) | F0/0: 211.241.14.1 / F0/1: 192.168.1.1 / Lo0: 220.220.1.1 |
| R5 | GIT-A | 지사 A (NHRP Client / Spoke) | F0/0: 211.241.25.2 / F0/1: 192.168.2.2 / Lo0: 220.220.2.2 |
| R6 | GIT-B | 지사 B (NHRP Client / Spoke) | F0/0: 211.241.36.3 / F0/1: 192.168.3.3 / Lo0: 220.220.3.3 |

---

## 3. 기본 구성 (Basic Configuration)

각 라우터의 hostname, loopback, frame-relay 기본 설정은  
[`configs/basic/`](./configs/basic/) 폴더를 참고하세요.

---

## 4. Chapter 1. 공중망 구성 (OSPF)

- **OSPF Process** : 1
- **Area** : 0
- **Router-ID** : SOL = X.X.X.X / ISP = XX.XX.XX.XX
- SOL-A, SOL-B의 사설망은 OSPF 제외
- ISP의 모든 네트워크 + Loopback 120/130은 OSPF 포함

👉 [`configs/chapter1-OSPF/`](./configs/chapter1-OSPF/)

---

## 5. Chapter 2. NAT Overload (PAT)

- SOL-A : 192.168.10.0/24 → 121.160.10.1 (Serial1/0.10) PAT
- SOL-B : 192.168.20.0/24 → 121.160.20.2 (Serial1/0.20) PAT

👉 [`configs/chapter2-NAT/`](./configs/chapter2-NAT/)

---

## 6. Chapter 3. GRE Tunnel (Static) — 물리 IP 기반

- Tunnel IP : **172.16.10.0/24**
- Tunnel Source/Dest : 물리 인터페이스 IP (Serial 1/0.10 ↔ Serial 1/0.20)
- 사설망 통신을 위해 **Static Route** 사용
- Dynagen으로 GRE 캡슐화 패킷 캡처 실습 포함

👉 [`configs/chapter3-GRE-Static/`](./configs/chapter3-GRE-Static/)

---

## 7. Chapter 4. GRE Tunnel (Dynamic) + EIGRP — Loopback 기반

- Tunnel IP : **172.16.100.0/24**
- Tunnel Source/Dest : **Loopback 0** IP (100.100.1.1 ↔ 100.100.2.2)
- EIGRP AS 100 으로 Tunnel 위 사설망 동적 라우팅

👉 [`configs/chapter4-GRE-Dynamic/`](./configs/chapter4-GRE-Dynamic/)

---

## 8. Chapter 5. MGRE + NHRP

> **MGRE (Multipoint GRE)** : 하나의 Tunnel Interface로 다수의 라우터를 Multi-access 구조로 연결  
> **NHRP** : MGRE 환경에서 Spoke의 Next-hop IP를 Hub에 동적으로 등록하기 위한 프로토콜

본 챕터는 두 가지 시나리오로 구성됩니다.

### 5-1. 시나리오 ① — ISP 라우터 간 MGRE (OSPF 공중망 + EIGRP 사설망)
- 공중망 : OSPF Process 1 / Area 0
- 사설망 : EIGRP AS 456
- Tunnel IP : 172.16.123.0/24
- Tunnel Source : **Loopback 0** IP
- NHRP Network-ID : **123**, Authentication : **cisco**
- Hub : ISP-1

### 5-2. 시나리오 ② — GIT 라우터 간 MGRE (EIGRP 공중망 + OSPF 사설망)
- 공중망 : EIGRP AS 2020
- 사설망 : OSPF Process 626 / Area 123
- Tunnel IP : 172.16.100.0/24
- Tunnel Source : **FastEthernet 0/0** IP
- NHRP Network-ID : **619**, Authentication : **soldesk**
- Hub : GIT-HQ
- Tunnel Network-Type 변경 실습 (point-to-multipoint / non-broadcast)

👉 [`configs/chapter5-MGRE/`](./configs/chapter5-MGRE/)

---

## 9. 패킷 캡슐화 구조 (Packet Encapsulation)

### GRE Static — SOL-A → SOL-B (ICMP Request)

```
==========================================================================
| FR DLCI | New IP Header     | GRE       | Original IP       | ICMP     |
--------------------------------------------------------------------------
|   102   | SA: 121.160.10.1  | Type      | SA: 172.16.10.1   | Type-8   |
|         | DA: 121.160.20.2  | 0x0800    | DA: 172.16.10.2   | Request  |
==========================================================================
```

### Static Route 적용 후 — PC1 → PC2 통신 흐름

```
[PC1] → [SOL-A] → [ISP 공중망] → [SOL-B] → [PC2]

PC1 → SOL-A (Ethernet)
=======================================================
| Ethernet     | IP                 | ICMP            |
| SA: PC1 Mac  | SA: 192.168.10.1   | Type-8 Request  |
| DA: G/W Mac  | DA: 192.168.20.2   |                 |
=======================================================

SOL-A → ISP 구간 (Frame-Relay + GRE 캡슐화)
=================================================================================
| FR DLCI | New IP            | GRE    | Original IP        | ICMP             |
| 103     | SA: 121.160.10.1  | 0x0800 | SA: 192.168.10.1   | Type-8 Request   |
|         | DA: 121.160.20.2  |        | DA: 192.168.20.2   |                  |
=================================================================================
```

---

## 10. 검증 명령어 (Verification)

```bash
show ip interface brief
show ip route
show ip route connected
show ip route eigrp
show ip ospf neighbor
show ip eigrp neighbors
show ip nat translation
show interface tunnel 12
show ip nhrp
show ip nhrp brief
show ip nhrp dynamic
show ip ospf interface tunnel 123
ping <destination>
ping <destination> source <source-ip>
traceroute <destination>
```

---

## 🔗 관련 저장소

- [Frame-Relay-Basic-Study](https://github.com/KSNAM97/Frame-Relay-Basic-Study)

## 🛠️ Tools Used

- GNS3 / Dynamips / Dynagen
- Cisco IOS (c7200)
- Wireshark (패킷 캡처)

## 📜 License

MIT License
