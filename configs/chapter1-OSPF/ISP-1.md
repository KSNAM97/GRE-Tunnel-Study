# Chapter 1. OSPF - ISP-1 (R3)

ISP-1 라우터의 모든 네트워크를 OSPF에 포함합니다.

## 적용 인터페이스

- Loopback 0 (100.100.11.0/24)
- Serial 1/0.10 (121.160.10.0/24)
- Serial 1/0.12 (121.160.12.0/24)

## Configuration

```cisco
router ospf 1
 router-id 11.11.11.11
 passive-interface default
 no passive-interface serial 1/0.10
 no passive-interface serial 1/0.12
 network 100.100.11.0  0.0.0.255 area 0
 network 121.160.10.0  0.0.0.255 area 0
 network 121.160.12.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
end
write memory
```
