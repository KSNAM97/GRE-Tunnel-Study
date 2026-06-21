# Chapter 5-4. Tunnel 위 OSPF 추가 + Network-Type 변경

MGRE Tunnel이 형성된 후, 사설망 라우팅을 위해 **Tunnel 인터페이스를 OSPF에 포함**합니다.  
다만 Tunnel은 기본적으로 **Point-to-Point** 타입이므로 Hub-Spoke 환경에서 **인접성이 1개만 맺어지는 문제**가 발생합니다.  
이를 해결하기 위해 **Network-Type을 변경**하는 두 가지 방법을 실습합니다.

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

### 문제점 (Network-Type 변경 전)

```bash
GIT-HQ# show ip ospf interface tunnel 123
  Network Type POINT_TO_POINT, Cost: 11111
  Neighbor Count is 1, Adjacent neighbor count is 1
  Adjacent with neighbor 3.3.3.3
```

→ Tunnel이 P2P이므로 Hub가 Spoke 중 **1개와만 인접성**을 맺음.

---

## 🔹 STEP 2-A. 방법 1 — `point-to-multipoint` 로 변경 (권장)

### GIT-HQ / GIT-A / GIT-B 공통

```cisco
interface tunnel 123
 ip ospf network point-to-multipoint
!
end
write memory
```

### ✅ 검증

```bash
GIT-HQ# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time  Address       Interface
2.2.2.2         0   FULL/  -    00:01:59   172.16.100.2  Tunnel123
3.3.3.3         0   FULL/  -    00:01:52   172.16.100.3  Tunnel123

GIT-A# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time  Address       Interface
1.1.1.1         0   FULL/  -    00:01:55   172.16.100.1  Tunnel123

GIT-B# show ip ospf neighbor
Neighbor ID   Pri   State       Dead Time  Address       Interface
1.1.1.1         0   FULL/  -    00:01:55   172.16.100.1  Tunnel123
```

### 사설망 라우팅 확인

```bash
GIT-HQ# show ip route | include 192
C 192.168.1.0/24 is directly connected, FastEthernet0/1
O 192.168.2.0/24 [110/11121] via 172.16.100.2, Tunnel123
O 192.168.3.0/24 [110/11121] via 172.16.100.3, Tunnel123

GIT-A# show ip route | include 192
O 192.168.1.0/24 [110/11121] via 172.16.100.1, Tunnel123
C 192.168.2.0/24 is directly connected, FastEthernet0/1
O 192.168.3.0/24 [110/22232] via 172.16.100.1, Tunnel123

GIT-B# show ip route | include 192
O 192.168.1.0/24 [110/11121] via 172.16.100.1, Tunnel123
O 192.168.2.0/24 [110/22232] via 172.16.100.1, Tunnel123
C 192.168.3.0/24 is directly connected, FastEthernet0/1
```

### End-to-End 통신 테스트

```bash
GIT-HQ# ping 192.168.2.2 source 192.168.1.1
GIT-HQ# ping 192.168.3.3 source 192.168.1.1
GIT-A#  ping 192.168.1.1 source 192.168.2.2
GIT-A#  ping 192.168.3.3 source 192.168.2.2
GIT-B#  ping 192.168.1.1 source 192.168.3.3
GIT-B#  ping 192.168.2.2 source 192.168.3.3
```

---

## 🔹 STEP 2-B. 방법 2 — `non-broadcast` (NBMA) 로 변경

NBMA에서는 **DR/BDR 선출**이 일어나며, **Neighbor 수동 지정**이 필요합니다.  
반드시 **Hub가 DR**, Spoke가 DROTHER가 되어야 합니다.

### GIT-HQ (Hub) — Priority 255 + Neighbor 수동 지정

```cisco
interface tunnel 123
 ip ospf network non-broadcast
 ip ospf priority 255
!
router ospf 626
 neighbor 172.16.100.2
 neighbor 172.16.100.3
!
end
```

### GIT-A / GIT-B (Spoke) — Priority 0 (DR 선출 제외)

```cisco
interface tunnel 123
 ip ospf network non-broadcast
 ip ospf priority 0
!
end
```

### ✅ 검증

```bash
GIT-HQ# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time  Address       Interface
3.3.3.3         0   FULL/DROTHER     00:01:51   172.16.100.3  Tunnel123
2.2.2.2         0   FULL/DROTHER     00:01:46   172.16.100.2  Tunnel123

GIT-A# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time  Address       Interface
1.1.1.1       255   FULL/DR          00:01:55   172.16.100.1  Tunnel123

GIT-B# show ip ospf neighbor
Neighbor ID   Pri   State            Dead Time  Address       Interface
1.1.1.1       255   FULL/DR          00:01:45   172.16.100.1  Tunnel123
```

---

## 📌 정리

| 방법 | OSPF Network-Type | 특징 | DR/BDR | Neighbor 명령어 |
|------|-------------------|------|--------|-----------------|
| 방법 1 | point-to-multipoint | 설정 간단, 권장 | ❌ 사용 안 함 | 불필요 |
| 방법 2 | non-broadcast | DR/BDR 선출, 수동 Neighbor 필요 | ⭕ 사용 | 필요 |
