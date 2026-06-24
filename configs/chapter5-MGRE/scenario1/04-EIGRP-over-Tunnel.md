# 📁 시나리오 ① - Step 4. Tunnel 위 EIGRP + Split-horizon 해제

MGRE Tunnel을 EIGRP에 포함시켜 사설망을 동적 학습합니다.  
**Hub-and-Spoke** 구조에서 Spoke 간 라우팅 정보 광고를 위해 **Split-horizon 해제**가 필요합니다.

---

## 🔹 STEP 1. ISP-1, ISP-2, ISP-3 — Tunnel을 EIGRP에 포함

```cisco
router eigrp 456
 no passive-interface tunnel 123
 network 172.16.123.0 0.0.0.255
!
end
```

### EIGRP Neighbor 확인

```bash
ISP-1# show ip eigrp neighbors
H  Address         Interface  Hold  Uptime    SRTT  RTO   Q  Seq
2  172.16.123.3    Tu123      10    00:33:56  237   5000  0  10
1  172.16.123.2    Tu123      13    00:33:59  187   5000  0  10
0  211.241.14.1    Fa0/0      11    00:35:14  658   3948  0  4

ISP-2# show ip eigrp neighbors
H  Address         Interface  Hold  Uptime    SRTT  RTO   Q  Seq
1  172.16.123.1    Tu123      10    00:33:45  1074  5000  0  14
0  211.241.25.2    Fa0/0      13    00:34:35  35    210   0  3

ISP-3# show ip eigrp neighbors
H  Address         Interface  Hold  Uptime    SRTT  RTO   Q  Seq
1  172.16.123.1    Tu123      10    00:33:27  1048  5000  0  14
0  211.241.36.3    Fa0/0      11    00:34:06  39    234   0  3
```

---

## 🔹 STEP 2. ISP-1 (Hub) — Split-horizon 해제

EIGRP는 기본적으로 Split-horizon이 **켜져 있어** Hub가 Spoke에서 받은 라우트를 다른 Spoke로 광고하지 않습니다.  
따라서 Hub의 Tunnel 인터페이스에서 Split-horizon을 해제해야 합니다.

```cisco
interface tunnel 123
 no ip split-horizon eigrp 456
!
end
```

---

## ✅ 최종 검증 — 사설망 상호 학습

```bash
GIT-HQ# show ip route | include 192
C    192.168.1.0/24 is directly connected, FastEthernet0/1
D    192.168.2.0/24
D    192.168.3.0/24

GIT-A# show ip route | include 192
D    192.168.1.0/24
C    192.168.2.0/24 is directly connected, FastEthernet0/1
D    192.168.3.0/24

GIT-B# show ip route | include 192
D    192.168.1.0/24
D    192.168.2.0/24
C    192.168.3.0/24 is directly connected, FastEthernet0/1
```

➡️ 모든 GIT 라우터가 서로의 사설망을 EIGRP로 학습 완료!

