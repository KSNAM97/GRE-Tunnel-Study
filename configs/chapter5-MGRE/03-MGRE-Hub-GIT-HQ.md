# Chapter 5-3. MGRE Hub (NHRP Server) - GIT-HQ (R4)

본사를 **MGRE Hub** + **NHRP Server**로 구성합니다.

## 주요 설정 요소

| 항목 | 값 |
|------|-----|
| Tunnel Interface | Tunnel 123 |
| Tunnel IP | 172.16.100.1/24 |
| Tunnel Source | 211.241.14.1 (F0/0) |
| Tunnel Mode | **gre multipoint** |
| NHRP Network-ID | 619 |
| NHRP Authentication | soldesk |
| Multicast Mapping | **dynamic** (Spoke 등록 시 자동 등록) |

## Configuration

```cisco
interface tunnel 123
 ip address 172.16.100.1 255.255.255.0
 tunnel source 211.241.14.1
 tunnel mode gre multipoint
 ip nhrp network-id 619
 ip nhrp authentication soldesk
 ip nhrp map multicast dynamic
!
end
write memory
```

## ✅ 검증

```bash
GIT-HQ# show ip nhrp dynamic
172.16.100.2/32 via 172.16.100.2, Tunnel123 created 00:03:12, expire 01:59:31
  Type: dynamic, Flags: authoritative unique registered used
  NBMA address: 211.241.25.2
172.16.100.3/32 via 172.16.100.3, Tunnel123 created 00:00:18, expire 01:59:41
  Type: dynamic, Flags: authoritative unique registered used
  NBMA address: 211.241.36.3

GIT-HQ# ping 172.16.100.2
GIT-HQ# ping 172.16.100.3
```
