# Chapter 5-2. 사설망 OSPF - GIT-HQ (R4)

본사(GIT-HQ)의 사설망과 Loopback 0을 **OSPF Process 626 / Area 123**에 포함합니다.  
Tunnel이 아직 생성되지 않은 상태이므로, 공중망 통신을 위해 **Default Route**를 ISP-1으로 설정합니다.

## 적용 인터페이스

- Loopback 0 (220.220.1.0/24)
- F0/1 (192.168.1.0/24) - 내부 사설망

## Configuration

```cisco
router ospf 626
 router-id 1.1.1.1
 passive-interface default
 network 192.168.1.0   0.0.0.255 area 123
 network 220.220.1.0   0.0.0.255 area 123
!
interface loopback 0
 ip ospf network point-to-point
!
! 공중망(ISP)으로의 Default Route
ip route 0.0.0.0 0.0.0.0 211.241.14.11
!
end
write memory
```

> ※ Tunnel이 아직 없으므로 OSPF Neighbor는 형성되지 않습니다.  
> Chapter 5-3 (MGRE) 구성 후 Tunnel 위에서 OSPF 인접성이 맺어집니다.
