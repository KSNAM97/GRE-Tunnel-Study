# 📁 시나리오 ② - Step 2. 사설망 OSPF + Default Route

GIT-HQ, GIT-A, GIT-B에 **OSPF**를 구성하고, Tunnel이 아직 없으므로 ISP 방향으로 **Default Route**를 추가합니다.

## 🎯 조건

- OSPF Process = **626**, Area = **123**
- Router-ID = 개별 설정
- Loopback 0 + 사설 네트워크 OSPF 포함
- 필요한 Interface로만 OSPF Packet 송신
- Interface Subnetmask로 광고

---

## GIT-HQ (R4)

```cisco
router ospf 626
 router-id 1.1.1.1
 passive-interface default
 network 192.168.1.0  0.0.0.255 area 123
 network 220.220.1.0  0.0.0.255 area 123
!
interface loopback 0
 ip ospf network point-to-point
!
ip route 0.0.0.0 0.0.0.0 211.241.14.11
!
end
```

## GIT-A (R5)

```cisco
router ospf 626
 router-id 2.2.2.2
 passive-interface default
 network 192.168.2.0  0.0.0.255 area 123
 network 220.220.2.0  0.0.0.255 area 123
!
interface loopback 0
 ip ospf network point-to-point
!
ip route 0.0.0.0 0.0.0.0 211.241.25.22
!
end
```

## GIT-B (R6)

```cisco
router ospf 626
 router-id 3.3.3.3
 passive-interface default
 network 192.168.3.0  0.0.0.255 area 123
 network 220.220.3.0  0.0.0.255 area 123
!
interface loopback 0
 ip ospf network point-to-point
!
ip route 0.0.0.0 0.0.0.0 211.241.36.33
!
end
```

> ⚠️ Tunnel이 아직 없으므로 OSPF Neighbor는 형성되지 않습니다.  
> Default Route만으로 공중망 통신을 검증합니다.

---

## ✅ 검증 — Routing Table

```bash
GIT-HQ# show ip route
Gateway of last resort is 211.241.14.11 to network 0.0.0.0
C    220.220.1.0/24 is directly connected, Loopback0
C    211.241.14.0/24 is directly connected, FastEthernet0/0
C    192.168.1.0/24 is directly connected, FastEthernet0/1
S*   0.0.0.0/0 [1/0] via 211.241.14.11

GIT-A# show ip route
Gateway of last resort is 211.241.25.22 to network 0.0.0.0
C    220.220.2.0/24 is directly connected, Loopback0
C    211.241.25.0/24 is directly connected, FastEthernet0/0
C    192.168.2.0/24 is directly connected, FastEthernet0/1
S*   0.0.0.0/0 [1/0] via 211.241.25.22

GIT-B# show ip route
Gateway of last resort is 211.241.36.33 to network 0.0.0.0
C    220.220.3.0/24 is directly connected, Loopback0
C    211.241.36.0/24 is directly connected, FastEthernet0/0
C    192.168.3.0/24 is directly connected, FastEthernet0/1
S*   0.0.0.0/0 [1/0] via 211.241.36.33
```

## ✅ 검증 — Ping Test (공중망 도달성)

```bash
! GIT-HQ
GIT-HQ# ping 211.241.25.2
GIT-HQ# ping 211.241.36.3

! GIT-A
GIT-A# ping 211.241.14.1
GIT-A# ping 211.241.36.3

! GIT-B
GIT-B# ping 211.241.14.1
GIT-B# ping 211.241.25.2
```
