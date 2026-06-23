# Chapter 1. OSPF - ISP-4 (R6)

ISP-4 라우터의 모든 네트워크 OSPF 포함.

## 적용 인터페이스

- Loopback 0 (100.100.14.0/24)
- Serial 1/0.20 (121.160.20.0/24)
- Serial 1/0.34 (121.160.34.0/24)

## Configuration

```cisco
router ospf 1
 router-id 44.44.44.44
 passive-interface default
 no passive-interface serial 1/0.20
 no passive-interface serial 1/0.34
 network 100.100.14.0  0.0.0.255 area 0
 network 121.160.20.0  0.0.0.255 area 0
 network 121.160.34.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
end
write memory
```
