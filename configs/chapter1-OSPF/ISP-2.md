# Chapter 1. OSPF - ISP-2 (R4)

ISP-2 라우터의 모든 네트워크 + Loopback 120 OSPF 포함.

## 적용 인터페이스

- Loopback 0 (100.100.12.0/24)
- Loopback 120 (120.20.2.0/24)
- Serial 1/0.12 (121.160.12.0/24)
- Serial 1/0.23 (121.160.23.0/24)

## Configuration

```cisco
router ospf 1
 router-id 22.22.22.22
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.23
 network 100.100.12.0  0.0.0.255 area 0
 network 121.160.12.0  0.0.0.255 area 0
 network 121.160.23.0  0.0.0.255 area 0
 network 120.20.2.0    0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
interface loopback 120
 ip ospf network point-to-point
!
end
write memory
```
