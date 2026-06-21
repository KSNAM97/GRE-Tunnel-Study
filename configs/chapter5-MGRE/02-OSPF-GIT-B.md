# Chapter 5-2. 사설망 OSPF - GIT-B (R6)

지사 B의 사설망과 Loopback 0을 **OSPF Process 626 / Area 123**에 포함합니다.

## 적용 인터페이스

- Loopback 0 (220.220.3.0/24)
- F0/1 (192.168.3.0/24) - 내부 사설망

## Configuration

```cisco
router ospf 626
 router-id 3.3.3.3
 passive-interface default
 network 192.168.3.0   0.0.0.255 area 123
 network 220.220.3.0   0.0.0.255 area 123
!
interface loopback 0
 ip ospf network point-to-point
!
! 공중망(ISP)으로의 Default Route
ip route 0.0.0.0 0.0.0.0 211.241.36.33
!
end
write memory
```

## ✅ 검증 (각 지사 → ISP F0/0 핑 테스트)

```bash
GIT-HQ# ping 211.241.25.2
GIT-HQ# ping 211.241.36.3
GIT-A#  ping 211.241.14.1
GIT-A#  ping 211.241.36.3
GIT-B#  ping 211.241.14.1
GIT-B#  ping 211.241.25.2
```
