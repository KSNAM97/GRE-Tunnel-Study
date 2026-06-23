# 📁 시나리오 ② - Step 4. Tunnel 위 OSPF + Network-Type 변경

MGRE Tunnel을 OSPF에 포함시켜 사설망을 동적 학습합니다.  
다만 Tunnel은 기본 **POINT_TO_POINT** 타입이므로 Hub-Spoke 환경에서 **인접성 1개만** 형성되는 문제가 발생합니다.

---

## 🔹 STEP 1. OSPF에 Tunnel 네트워크 추가

### GIT-HQ / GIT-A / GIT-B 공통

```cisco
router ospf 626
 no passive-interface tunnel 123
 network 172.16.100.0 0.0.0.255 area 123
!
end
```

### 문제점 확인

```bash
GIT-HQ# show ip ospf interface tunnel 123
Tunnel123 is up, line protocol is up
  Internet Address 172.16.100.1/24, Area 123
  Process ID 626, Router ID 1.1.1.1, Network Type POINT_TO_POINT, Cost: 11111
  Transmit Delay is 1 sec, State POINT_TO_POINT
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
    oob-resync timeout 40
    Hello due in 00:00:02
  Supports Link-local Signaling (LLS)
  Cisco NSF helper support enabled
  IETF NSF helper support enabled
  Index 3/3, flood queue length 0
  Next 0x0(0)/0x0(0)
  Last flood scan length is 1, maximum is 1
  Last flood scan time is 4 msec, maximum is 4 msec
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 3.3.3.3
  Suppress hello for 0 neighbor(s)
```

➡️ Tunnel이 P2P이므로 Hub가 Spoke 중 **1개와만 인접성**을 맺음.  
➡️ 해결책 : **Network-Type을 Multi-access 구조로 변경**

---

## 🔹 STEP 2-A. 방법 ① — point-to-multipoint (권장)

### GIT-HQ / GIT-A / GIT-B 공통

```cisco
interface tunnel 123
 ip ospf network point-to-multipoint
!
end
```

### ✅ Neighbor 확인

```bash
GIT-HQ# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time   Address       Interface
2.2.2.2         0   FULL/  -    00:01:59    172.16.100.2  Tunnel123
3.3.3.3         0   FULL/  -    00:01:52    172.16.100.3  Tunnel123

GIT-A# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time   Address       Interface
1.1.1.1         0   FULL/  -    00:01:55    172.16.100.1  Tunnel123

GIT-B# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time   Address       Interface
1.1.1.1         0   FULL/  -    00:01:55    172.16.100.1  Tunnel123
```

### ✅ 사설망 라우팅 확인

```bash
GIT-HQ# show ip route | include 192
C    192.168.1.0/24 is directly connected, FastEthernet0/1
O    192.168.2.0/24 [110/11121] via 172.16.100.2, 00:00:31, Tunnel123
O    192.168.3.0/24 [110/11121] via 172.16.100.3, 00:00:31, Tunnel123

GIT-A# show ip route | include 192
O    192.168.1.0/24 [110/11121] via 172.16.100.1, 00:00:41, Tunnel123
C    192.168.2.0/24 is directly connected, FastEthernet0/1
O    192.168.3.0/24 [110/22232] via 172.16.100.1, 00:00:41, Tunnel123

GIT-B# show ip route | include 192
O    192.168.1.0/24 [110/11121] via 172.16.100.1, 00:00:41, Tunnel123
O    192.168.2.0/24 [110/22232] via 172.16.100.1, 00:00:41, Tunnel123
C    192.168.3.0/24 is directly connected, FastEthernet0/1
```

### ✅ End-to-End 통신 테스트

```bash
GIT-HQ# ping 192.168.2.2 source 192.168.1.1
GIT-HQ# ping 192.168.3.3 source 192.168.1.1
GIT-A#  ping 192.168.1.1 source 192.168.2.2
GIT-A#  ping 192.168.3.3 source 192.168.2.2
GIT-B#  ping 192.168.1.1 source 192.168.3.3
GIT-B#  ping 192.168.2.2 source 192.168.3.3
```

---

## 🔹 STEP 2-B. 방법 ② — non-broadcast (NBMA)

NBMA에서는 **DR/BDR 선출**이 일어나며, **Neighbor 수동 지정**이 필요합니다.  
반드시 **Hub가 DR**, Spoke가 DROTHER가 되어야 합니다.

### GIT-HQ / GIT-A / GIT-B 공통

```cisco
interface tunnel 123
 ip ospf network non-broadcast
!
end
```

### GIT-HQ (Hub) 추가 설정 — Priority 255 + Neighbor 수동 지정

```cisco
interface tunnel 123
 ip ospf priority 255
!
router ospf 626
 neighbor 172.16.100.2
 neighbor 172.16.100.3
!
end
```

### GIT-A / GIT-B (Spoke) 추가 설정 — Priority 0 (DR 선출 제외)

```cisco
interface tunnel 123
 ip ospf priority 0
!
end
```

### ✅ 검증

```bash
GIT-HQ# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time   Address       Interface
3.3.3.3         0   FULL/DROTHER     00:01:51    172.16.100.3  Tunnel123
2.2.2.2         0   FULL/DROTHER     00:01:46    172.16.100.2  Tunnel123

GIT-A# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time   Address       Interface
1.1.1.1       255   FULL/DR          00:01:55    172.16.100.1  Tunnel123

GIT-B# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time   Address       Interface
1.1.1.1       255   FULL/DR          00:01:45    172.16.100.1  Tunnel123
```

---

## 📌 두 방법 비교

| 항목 | 방법 ① point-to-multipoint | 방법 ② non-broadcast |
|------|---------------------------|---------------------|
| Network-Type | point-to-multipoint | non-broadcast (NBMA) |
| DR/BDR 선출 | ❌ 사용 안 함 | ⭕ 사용 |
| Neighbor 수동 지정 | 불필요 | **필요** (Hub에서) |
| Priority 설정 | 불필요 | Hub=255, Spoke=0 |
| 설정 복잡도 | ⭐ 간단 (권장) | ⭐⭐⭐ 복잡 |
| Hello 방식 | Multicast | Unicast |
