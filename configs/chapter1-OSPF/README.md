# 📁 Chapter 1. 공중망 구성 (OSPF)

Frame-Relay 백본 위에 **OSPF**를 사용하여 공중망(Public Network)을 구성합니다.

## 🎯 조건

- OSPF Process = 1, Area = 0
- Router-ID : SOL = X.X.X.X / ISP = XX.XX.XX.XX
- SOL-A, SOL-B의 **사설 네트워크는 OSPF 제외**
- SOL-A, SOL-B의 Serial, Loopback 0 네트워크는 OSPF 포함
- ISP-1 ~ ISP-4의 모든 네트워크는 OSPF 포함
- ISP-2의 Loopback 120, ISP-3의 Loopback 130은 OSPF 포함
- Interface 할당된 SubnetMask로 광고
- OSPF 업데이트가 필요한 Interface로만 OSPF Packet 송신 (`passive-interface default`)

## 파일 목록

| 파일 | 장비 |
|------|------|
| [SOL-A.md](./SOL-A.md) | R1 |
| [SOL-B.md](./SOL-B.md) | R2 |
| [ISP-1.md](./ISP-1.md) | R3 |
| [ISP-2.md](./ISP-2.md) | R4 |
| [ISP-3.md](./ISP-3.md) | R5 |
| [ISP-4.md](./ISP-4.md) | R6 |

## ✅ 검증

```bash
SOL-A# show ip ospf neighbor   [인접성 1개]
SOL-B# show ip ospf neighbor   [인접성 1개]
ISP-1# show ip ospf neighbor   [인접성 2개]
ISP-2# show ip ospf neighbor   [인접성 2개]
ISP-3# show ip ospf neighbor   [인접성 2개]
ISP-4# show ip ospf neighbor   [인접성 2개]

SOL-A# ping 120.20.2.2
SOL-A# ping 130.30.3.3
SOL-A# ping 100.100.20.2

SOL-B# ping 120.20.2.2
SOL-B# ping 130.30.3.3
SOL-B# ping 100.100.10.1
```
