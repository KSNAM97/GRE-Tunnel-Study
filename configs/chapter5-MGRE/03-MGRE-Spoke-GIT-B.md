# Chapter 5-3. MGRE Spoke (NHRP Client) - GIT-B (R6)

지사 B를 **MGRE Spoke** + **NHRP Client**로 구성합니다.

## 주요 설정 요소

| 항목 | 값 |
|------|-----|
| Tunnel Interface | Tunnel 123 |
| Tunnel IP | 172.16.100.3/24 |
| Tunnel Source | 211.241.36.3 (F0/0) |
| Tunnel Mode | gre multipoint |
| NHRP Network-ID | 619 |
| NHRP Authentication | soldesk |
| NHRP Server (NHS) | 172.16.100.1 (Hub Tunnel IP) |
| Static Mapping | 172.16.100.1 ↔ 211.241.14.1 (Hub) |

## Configuration

```cisco
interface tunnel 123
 ip address 172.16.100.3 255.255.255.0
 tunnel source 211.241.36.3
 tunnel mode gre multipoint
 ip nhrp network-id 619
 ip nhrp authentication soldesk
 ip nhrp nhs 172.16.100.1
 ip nhrp map multicast 211.241.14.1
 ip nhrp map 172.16.100.1 211.241.14.1
!
end
write memory
```

## ✅ 검증

```bash
GIT-B# show ip nhrp brief
Target          Via          NBMA          Mode    Intfc   Claimed
172.16.100.1/32 172.16.100.1 211.241.14.1  static  Tu123   < >

GIT-B# ping 172.16.100.1
GIT-B# ping 172.16.100.2   ! Spoke ↔ Spoke 통신
```
