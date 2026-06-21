# Chapter 4. GRE Tunnel (Dynamic) - SOL-A (R1)

Loopback 0 기반 GRE Tunnel + EIGRP 동적 라우팅 구성.

## 주요 설정 요소

- Tunnel 12 : 172.16.100.1/24
- Source : 100.100.1.1 (Loopback 0)
- Destination : 100.100.2.2 (SOL-B Loopback 0)
- EIGRP AS 100 : 사설망 + Loopback 1 + Tunnel 포함

## Configuration

```cisco
! ---------- Tunnel 생성 ----------
interface tunnel 12
 ip address 172.16.100.1 255.255.255.0
 tunnel source 100.100.1.1
 tunnel destination 100.100.2.2
!
! ---------- EIGRP 사설망 라우팅 ----------
router eigrp 100
 no auto-summary
 passive-interface default
 no passive-interface tunnel 12
 network 192.168.10.0
 network 100.100.10.0 0.0.0.255
 network 172.16.100.0 0.0.0.255
!
end
write memory
```
