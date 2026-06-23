# 📁 시나리오 ① - Step 1. 공중망 OSPF 구성

ISP-1, ISP-2, ISP-3 구간을 **OSPF**로 공중망 구성합니다.

## 🎯 조건

- OSPF Process = 1, Area = 0
- Router-ID = 13.13.X.X (X = Router 번호)
- ISP의 Serial Interface 네트워크는 OSPF 포함
- ISP의 Loopback 0 네트워크는 OSPF 포함
- Interface SubnetMask로 광고
- 필요한 Interface로만 OSPF Packet 송신

## ISP-1 (R1)

```cisco
router ospf 1
 router-id 13.13.1.1
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.13
 network 110.110.1.0   0.0.0.255 area 0
 network 198.210.12.0  0.0.0.255 area 0
 network 198.210.13.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
end
```

## ISP-2 (R2)

```cisco
router ospf 1
 router-id 13.13.2.2
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.23
 network 110.110.2.0   0.0.0.255 area 0
 network 198.210.12.0  0.0.0.255 area 0
 network 198.210.23.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
end
```

## ISP-3 (R3)

```cisco
router ospf 1
 router-id 13.13.3.3
 passive-interface default
 no passive-interface serial 1/0.13
 no passive-interface serial 1/0.23
 network 110.110.3.0   0.0.0.255 area 0
 network 198.210.13.0  0.0.0.255 area 0
 network 198.210.23.0  0.0.0.255 area 0
!
interface loopback 0
 ip ospf network point-to-point
!
end
```

## ✅ 검증

```bash
ISP-1# show ip ospf neighbor
Neighbor ID  Pri  State    Dead Time  Address       Interface
13.13.2.2     0   FULL/ -  00:00:37   198.210.12.2  Serial1/0.12
13.13.3.3     0   FULL/ -  00:00:37   198.210.13.3  Serial1/0.13

ISP-2# show ip ospf neighbor
Neighbor ID  Pri  State    Dead Time  Address       Interface
13.13.1.1     0   FULL/ -  00:00:37   198.210.12.1  Serial1/0.12
13.13.3.3     0   FULL/ -  00:00:37   198.210.23.3  Serial1/0.23

ISP-3# show ip ospf neighbor
Neighbor ID  Pri  State    Dead Time  Address       Interface
13.13.1.1     0   FULL/ -  00:00:37   198.210.13.1  Serial1/0.13
13.13.2.2     0   FULL/ -  00:00:37   198.210.23.2  Serial1/0.23
```
