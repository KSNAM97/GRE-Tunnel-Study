# Chapter 1. OSPF - SOL-B (R2)

지사 Gateway에 OSPF Process 1 / Area 0 으로 공중망 구성.

## 적용 인터페이스

- Loopback 0 (100.100.2.0/24)
- Serial 1/0.20 (121.160.20.0/24)

> ※ Loopback 1 (100.100.20.0/24) 및 사설망 (192.168.20.0/24)은 OSPF 미포함

## Configuration

```cisco
router ospf 1
 router-id 2.2.2.2
 passive-interface default
 no passive-interface serial 1/0.20
 network 100.100.2.0   0.0.0.255 area 0
 network 121.160.20.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
interface loopback 1
 ip ospf network point-to-point
!
end
write memory
```
