# 📁 Chapter 5. MGRE + NHRP

![MGRE Topology](/topology/MGRE_Topology.png)

## 🎯 핵심 개념

### MGRE (Multipoint GRE)
- 일반 GRE Tunnel : Point-to-Point → 지사 N개 연결 시 본사에 Tunnel N개 필요
- GRE Tunnel 1개당 약 **9Kbps 대역폭 소모** → 다수 생성 시 대역폭 손실 큼
- **MGRE** : 하나의 Tunnel Interface로 Multi-Access 구조 연결 → 본사에 Tunnel 1개만 생성
- 기본 Network-Type : **NBMA**

### NHRP (Next-Hop Resolution Protocol)
- Tunnel의 기본 Network Type은 Point-to-Point이므로 다수의 Router를 하나의 Tunnel로 연결할 수 없음
- MGRE 구성 시 NHRP를 사용하여 다수의 Router 간 **Next-hop을 동적으로 연결**
- **NHRP Server** (Hub) : Dynamic하게 NHRP Client의 등록 요청을 대기
- **NHRP Client** (Spoke) : NHRP Server에게 자신의 IP 주소를 등록할 것을 요청
- Server는 Client로부터 요청이 수신되면 **Network-ID 및 인증 조건**을 확인 후 Client IP를 등록

---

## 📚 본 챕터의 두 가지 시나리오

| 구분 | 시나리오 ① | 시나리오 ② |
|------|-----------|-----------|
| Hub 역할 | **ISP-1** (R1) | **GIT-HQ** (R4) |
| Spoke | ISP-2, ISP-3 | GIT-A, GIT-B |
| 공중망 라우팅 | OSPF Process 1 | EIGRP AS 2020 |
| 사설망 라우팅 | EIGRP AS 456 | OSPF Process 626 |
| Tunnel IP | 172.16.123.0/24 | 172.16.100.0/24 |
| Tunnel Source | **Loopback 0** | **FastEthernet 0/0** |
| NHRP Network-ID | 123 | 619 |
| NHRP Auth | cisco | soldesk |
| 추가 학습 | Split-horizon 해제 | OSPF Network-Type 변경 |

---

## 📂 파일 목록

### 시나리오 ① (ISP 라우터 간 MGRE)
| 파일 | 내용 |
|------|------|
| [scenario1/01-OSPF-Public.md](./scenario1/01-OSPF-Public.md) | 공중망 OSPF 구성 |
| [scenario1/02-EIGRP-Private.md](./scenario1/02-EIGRP-Private.md) | 사설망 EIGRP 구성 |
| [scenario1/03-MGRE-NHRP.md](./scenario1/03-MGRE-NHRP.md) | MGRE Tunnel + NHRP |
| [scenario1/04-EIGRP-over-Tunnel.md](./scenario1/04-EIGRP-over-Tunnel.md) | Tunnel 위 EIGRP + Split-horizon 해제 |

### 시나리오 ② (GIT 라우터 간 MGRE)
| 파일 | 내용 |
|------|------|
| [scenario2/01-EIGRP-Public.md](./scenario2/01-EIGRP-Public.md) | 공중망 EIGRP 구성 |
| [scenario2/02-OSPF-Private.md](./scenario2/02-OSPF-Private.md) | 사설망 OSPF + Default Route |
| [scenario2/03-MGRE-NHRP.md](./scenario2/03-MGRE-NHRP.md) | MGRE Tunnel + NHRP |
| [scenario2/04-OSPF-over-Tunnel.md](./scenario2/04-OSPF-over-Tunnel.md) | Tunnel 위 OSPF + Network-Type 변경 |
