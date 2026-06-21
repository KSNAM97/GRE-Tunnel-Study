# 📁 Chapter 5. MGRE + NHRP 구성

하나의 Tunnel Interface로 다수의 지사(Spoke)를 동시에 연결하는 **MGRE (Multipoint GRE)** 환경을 구성합니다.

## 🎯 핵심 개념

### MGRE (Multipoint GRE)
- 일반 GRE Tunnel : Point-to-Point → 지사 N개 연결 시 본사에 Tunnel N개 필요
- GRE Tunnel 1개당 약 **9Kbps 대역폭 소모** → 다수 생성 시 대역폭 손실 큼
- **MGRE** : 하나의 Tunnel Interface로 Multi-Access 구조 연결 → 본사에 Tunnel 1개만 생성
- 기본 Network-Type : **NBMA**

### NHRP (Next-Hop Resolution Protocol)
- MGRE는 Multi-Access 구조이므로 Spoke의 **Next-hop IP를 동적으로 알아내야 함**
- `NHRP Server` (Hub) : Spoke의 등록 요청을 대기
- `NHRP Client` (Spoke) : 자신의 IP를 Server에 등록 요청
- Network-ID + 인증(Authentication) 조건 충족 시 등록 완료

## 🏗️ 구성 시나리오

```
                       [ ISP-1 ]
                          |
                       [ GIT-HQ ]  ◀── NHRP Server (Hub)
                       Tunnel123 (172.16.100.1)
                       /          \
                      /            \
               [ ISP-2 ]          [ ISP-3 ]
                  |                   |
              [ GIT-A ]            [ GIT-B ]
          (172.16.100.2)        (172.16.100.3)
           NHRP Client            NHRP Client
              (Spoke)               (Spoke)
```

## 🎯 조건 요약

| 항목 | 값 |
|------|-----|
| 공중망 라우팅 | EIGRP AS 2020 |
| 사설망 라우팅 | OSPF Process 626 / Area 123 |
| Tunnel IP | 172.16.100.0/24 |
| Tunnel Source | F0/0 IP 사용 |
| Tunnel Mode | gre multipoint |
| NHRP Network-ID | 619 |
| NHRP Authentication | soldesk |
| Hub (NHRP Server) | GIT-HQ (R4) |
| Spoke (NHRP Client) | GIT-A (R5), GIT-B (R6) |

## 📂 파일 목록

| 파일 | 장비 | 설명 |
|------|------|------|
| [01-EIGRP-ISP-1.md](./01-EIGRP-ISP-1.md) | R1 | 공중망 EIGRP (ISP-1) |
| [01-EIGRP-ISP-2.md](./01-EIGRP-ISP-2.md) | R2 | 공중망 EIGRP (ISP-2) |
| [01-EIGRP-ISP-3.md](./01-EIGRP-ISP-3.md) | R3 | 공중망 EIGRP (ISP-3) |
| [02-OSPF-GIT-HQ.md](./02-OSPF-GIT-HQ.md) | R4 | 사설망 OSPF + Default Route |
| [02-OSPF-GIT-A.md](./02-OSPF-GIT-A.md) | R5 | 사설망 OSPF + Default Route |
| [02-OSPF-GIT-B.md](./02-OSPF-GIT-B.md) | R6 | 사설망 OSPF + Default Route |
| [03-MGRE-Hub-GIT-HQ.md](./03-MGRE-Hub-GIT-HQ.md) | R4 | MGRE Hub (NHRP Server) |
| [03-MGRE-Spoke-GIT-A.md](./03-MGRE-Spoke-GIT-A.md) | R5 | MGRE Spoke (NHRP Client) |
| [03-MGRE-Spoke-GIT-B.md](./03-MGRE-Spoke-GIT-B.md) | R6 | MGRE Spoke (NHRP Client) |
| [04-OSPF-over-Tunnel.md](./04-OSPF-over-Tunnel.md) | R4/R5/R6 | Tunnel 위 OSPF 추가 + Network-Type 변경 |

## ✅ 검증

```bash
! NHRP 등록 상태 확인
GIT-HQ# show ip nhrp dynamic

! OSPF 인접성 확인 (Network-type 변경 후)
GIT-HQ# show ip ospf neighbor

! 사설망 라우팅 확인
GIT-HQ# show ip route | include 192
GIT-A#  show ip route | include 192
GIT-B#  show ip route | include 192

! 사설망 통신 테스트
GIT-HQ# ping 192.168.2.2 source 192.168.1.1
GIT-HQ# ping 192.168.3.3 source 192.168.1.1
GIT-A#  ping 192.168.1.1 source 192.168.2.2
GIT-A#  ping 192.168.3.3 source 192.168.2.2
GIT-B#  ping 192.168.1.1 source 192.168.3.3
GIT-B#  ping 192.168.2.2 source 192.168.3.3
```
