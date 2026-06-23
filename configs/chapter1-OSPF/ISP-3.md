# Chapter 1. OSPF - ISP-3 (R5)

ISP-3 라우터의 모든 네트워크 + Loopback 130 OSPF 포함.

## 적용 인터페이스

- Loopback 0 (100.100.13.0/24)
- Loopback 130 (130.30.3.0/24)
- Serial 1/0.23 (121.160.23.0/24)
- Serial 1/0.34 (121.160.34.0/24)

## Configuration

```cisco
router ospf 1
 router-id 33.33.33.33
 passive-interface default
 no passive-interface serial 1/0.23
 no passive-interface serial 1/0.34
 network 100.100.13.0  0.0.0.255 area 0
 network 121.160.23.0  0.0.0.255 area 0
 network 121.160.34.0  0.0.0.255 area 0
 network 130.30.3.0    0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
interface loopback 130
 ip ospf network point-to-point
!
end
write memory
```
