# 📁 Chapter 3. GRE Tunnel (Static) 구성

SOL-A와 SOL-B 간을 **GRE Tunnel**로 연결하고, 사설망 통신은 **Static Route**로 처리합니다.

## 🎯 조건

- Tunnel Network : **172.16.10.0/24**
- Tunnel Source / Destination : 물리 Interface IP 사용
  - SOL-A ↔ ISP-1 : 121.160.10.1
  - SOL-B ↔ ISP-4 : 121.160.20.2

## 동작 원리

- Tunnel interface 생성 후 `tunnel source` 설정 시점 → Layer 2 Down
- `tunnel destination` 설정 후 Routing Table에 목적지가 있으면 → Layer 2 Up
- 즉, **Tunnel destination이 Routing Table에서 확인되어야 활성화**

## 파일 목록

| 파일 | 장비 |
|------|------|
| [SOL-A.md](./SOL-A.md) | R1 |
| [SOL-B.md](./SOL-B.md) | R2 |

## ✅ 검증

```bash
SOL-A# show ip route connected
C 172.16.10.0   is directly connected, Tunnel12
C 192.168.10.0/24 is directly connected, FastEthernet0/0
S 192.168.20.0/24 [1/0] via 172.16.10.2

SOL-A# ping 172.16.10.2
SOL-B# ping 172.16.10.1
PC1# ping 192.168.20.2
PC2# ping 192.168.10.1
```

## ⚠️ ISP 라우터에는 사설 IP (172.16.10.0/24)가 보이지 않음

```bash
ISP-1# show ip route   ← 172.16.10.0/24 없음
ISP-2# show ip route   ← 172.16.10.0/24 없음
ISP-3# show ip route   ← 172.16.10.0/24 없음
ISP-4# show ip route   ← 172.16.10.0/24 없음
```
