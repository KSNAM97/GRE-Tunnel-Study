# 📁 Chapter 1. 공중망 구성 (OSPF)

Frame-Relay 백본 위에 **OSPF**를 사용하여 공중망(Public Network)을 구성합니다.

## 🎯 조건

- OSPF Process = 1, Area = 0
- Router-ID : SOL = X.X.X.X / ISP = XX.XX.XX.XX
- SOL-A, SOL-B의 **사설 네트워크는 OSPF 제외**
- SOL-A, SOL-B의 Serial, Loopback 0 네트워크는 OSPF 포함
- ISP-1 ~ ISP-4의 모든 네트워크는 OSPF 포함
- ISP-2의 Loopback 120, ISP-3의 Loopback 130은 OSPF 포함
- Interface에 할당된 SubnetMask로 광고
- OSPF Update가 필요한 Interface로만 송신 (`passive-interface default`)

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

### Neighbor 확인

```bash
SOL-A# show ip ospf neighbor   [인접성 1개]
Neighbor ID     Pri   State           Dead Time   Address         Interface
11.11.11.11       0   FULL/  -        00:00:33    121.160.10.11   Serial1/0.10

SOL-B# show ip ospf neighbor   [인접성 1개]
Neighbor ID     Pri   State           Dead Time   Address         Interface
44.44.44.44       0   FULL/  -        00:00:37    121.160.20.4    Serial1/0.20

ISP-1# show ip ospf neighbor   [인접성 2개]
Neighbor ID     Pri   State           Dead Time   Address         Interface
22.22.22.22       0   FULL/  -        00:00:30    121.160.12.2    Serial1/0.12
1.1.1.1           0   FULL/  -        00:00:30    121.160.10.1    Serial1/0.10

ISP-2# show ip ospf neighbor   [인접성 2개]
ISP-3# show ip ospf neighbor   [인접성 2개]
ISP-4# show ip ospf neighbor   [인접성 2개]
```

### Ping Test

```bash
SOL-A# ping 120.20.2.2          ! ISP-2 Loopback 120
SOL-A# ping 130.30.3.3          ! ISP-3 Loopback 130
SOL-A# ping 100.100.20.2        ! SOL-B Loopback 1

SOL-B# ping 120.20.2.2
SOL-B# ping 130.30.3.3
SOL-B# ping 100.100.10.1        ! SOL-A Loopback 1
```
