# 📁 시나리오 ① - Step 2. 사설망 EIGRP 구성

GIT-HQ ↔ ISP-1, GIT-A ↔ ISP-2, GIT-B ↔ ISP-3 구간을 **EIGRP**로 연결합니다.

## 🎯 조건

- EIGRP AS = 456
- `no auto-summary`
- GIT-HQ, GIT-A, GIT-B의 모든 네트워크는 EIGRP 포함
- ISP의 F0/0, Loopback 1 네트워크는 EIGRP 포함
- 필요한 Interface로만 EIGRP Packet 송신

---

## 🔹 ISP-1 ↔ GIT-HQ 구간

### GIT-HQ (R4)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fa0/0
 network 211.241.14.0
 network 192.168.1.0
 network 220.220.1.0
!
end
```

### ISP-1 (R1)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fa0/0
 network 211.241.14.0
 network 110.110.10.0 0.0.0.255
!
end
```

### 검증

```bash
GIT-HQ# show ip eigrp neighbor
H  Address          Interface  Hold  Uptime    SRTT  RTO   Q  Seq
0  211.241.14.11    Fa0/0      13    00:02:38  69    414   0  4

ISP-1# show ip route eigrp
D    220.220.1.0/24 [90/409600] via 211.241.14.1, FastEthernet0/0
D    192.168.1.0/24 [90/307200] via 211.241.14.1, FastEthernet0/0

ISP-1# ping 192.168.1.1
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/26/32 ms

GIT-HQ# show ip route eigrp
     110.0.0.0/24 is subnetted, 1 subnets
D       110.110.10.0 [90/409600] via 211.241.14.11, FastEthernet0/0
```

---

## 🔹 ISP-2 ↔ GIT-A 구간

### ISP-2 (R2)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fastethernet 0/0
 network 110.110.20.0  0.0.0.255
 network 211.241.25.0
!
end
```

### GIT-A (R5)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fastethernet 0/0
 network 220.220.2.0
 network 211.241.25.0
 network 192.168.2.0
!
end
```

### 검증

```bash
ISP-2# show ip eigrp neighbor
H  Address       Interface  Hold  Uptime    SRTT  RTO   Q  Seq
0  211.241.25.2  Fa0/0      13    00:00:34  54    324   0  3

GIT-A# show ip eigrp neighbor
H  Address         Interface  Hold  Uptime    SRTT  RTO   Q  Seq
0  211.241.25.22   Fa0/0      10    00:01:17  1279  5000  0  4

ISP-2# show ip route eigrp
D    220.220.2.0/24 [90/409600] via 211.241.25.2, FastEthernet0/0
D    192.168.2.0/24 [90/307200] via 211.241.25.2, FastEthernet0/0

ISP-2# ping 192.168.2.2

GIT-A# show ip route eigrp
     110.0.0.0/24 is subnetted, 1 subnets
D       110.110.20.0 [90/307200] via 211.241.25.22, FastEthernet0/0
```

---

## 🔹 ISP-3 ↔ GIT-B 구간

### ISP-3 (R3)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fastethernet 0/0
 network 110.110.30.0  0.0.0.255
 network 211.241.36.0
!
end
```

### GIT-B (R6)

```cisco
router eigrp 456
 no auto-summary
 passive-interface default
 no passive-interface fastethernet 0/0
 network 220.220.3.0
 network 211.241.36.0
 network 192.168.3.0
!
end
```

### 검증

```bash
ISP-3# show ip eigrp neighbor
H  Address        Interface  Hold  Uptime    SRTT  RTO   Q  Seq
0  211.241.36.3   Fa0/0      10    00:01:19  53    318   0  3

GIT-B# show ip eigrp neighbor
H  Address         Interface  Hold  Uptime    SRTT  RTO   Q  Seq
0  211.241.36.33   Fa0/0      13    00:01:50  1293  5000  0  4

ISP-3# show ip route eigrp
D    220.220.3.0/24 [90/409600] via 211.241.36.3, FastEthernet0/0
D    192.168.3.0/24 [90/307200] via 211.241.36.3, FastEthernet0/0

ISP-3# ping 192.168.3.3

GIT-B# show ip route eigrp
     110.0.0.0/24 is subnetted, 1 subnets
D       110.110.30.0 [90/307200] via 211.241.36.33, FastEthernet0/0
```
