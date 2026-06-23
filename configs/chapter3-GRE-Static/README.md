# 📁 Chapter 3. GRE Tunnel (Static) — 물리 IP 기반

SOL-A와 SOL-B 간을 **GRE Tunnel**로 연결하고, 사설망 통신은 **Static Route**로 처리합니다.

## 🎯 조건

- Tunnel Network : **172.16.10.0/24**
- Tunnel Source / Destination : **물리 Interface IP** 사용
  - SOL-A : Serial 1/0.10 (121.160.10.1) ↔ ISP-1 직결
  - SOL-B : Serial 1/0.20 (121.160.20.2) ↔ ISP-4 직결

## 🔍 Tunnel 동작 원리

| 단계 | 동작 | Layer 2 상태 |
|------|------|-------------|
| 1 | Tunnel Interface 생성만 | **Down** |
| 2 | `tunnel source X.X.X.X` 설정 | **Down** |
| 3 | `tunnel destination X.X.X.X` 설정 | **Up** |

➡️ Tunnel은 **Destination IP가 Routing Table에서 확인되는 환경에서만 활성화**됩니다.

## 📂 파일 목록

| 파일 | 단계 |
|------|------|
| [01-Tunnel-SOL-A.md](./01-Tunnel-SOL-A.md) | SOL-A Tunnel 생성 |
| [01-Tunnel-SOL-B.md](./01-Tunnel-SOL-B.md) | SOL-B Tunnel 생성 |
| [02-StaticRoute-SOL-A.md](./02-StaticRoute-SOL-A.md) | SOL-A 사설망 Static Route |
| [02-StaticRoute-SOL-B.md](./02-StaticRoute-SOL-B.md) | SOL-B 사설망 Static Route |
| [03-Packet-Analysis.md](./03-Packet-Analysis.md) | Dynagen 패킷 캡처 + 분석 |

## ⚠️ 중요 — ISP 라우터에는 사설망이 보이지 않음

ISP-1 ~ ISP-4 라우터의 Routing Table에는 사설 IP `172.16.10.0/24` 가 절대로 나타나지 않습니다.  
이는 GRE Tunnel이 양 끝단(SOL-A ↔ SOL-B)에서만 캡슐화/디캡슐화되는 **End-to-End Tunnel**이기 때문입니다.

```bash
ISP-1# show ip route   ! ❌ 172.16.10.0/24 없음
ISP-2# show ip route   ! ❌ 172.16.10.0/24 없음
ISP-3# show ip route   ! ❌ 172.16.10.0/24 없음
ISP-4# show ip route   ! ❌ 172.16.10.0/24 없음
```

## ⚠️ Static Route 적용 전 — 사설망 통신 실패 사례

Tunnel만 생성하고 Static Route가 없으면 사설망 간 통신은 실패합니다.

```bash
PC1# ping 192.168.20.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.20.2, timeout is 2 seconds:
U.U.U
Success rate is 0 percent (0/5)
```

SOL-A의 Routing Table에 SOL-B 사설망(192.168.20.0/24) 정보가 없기 때문입니다.

```bash
SOL-A# show ip route
C    192.168.10.0/24 is directly connected, FastEthernet0/0
C    172.16.10.0   is directly connected, Tunnel12
! ❌ 192.168.20.0/24 정보 없음 → 통신 불가
```

➡️ 해결책 : **Static Route를 Tunnel을 통해 추가** (02 단계)
