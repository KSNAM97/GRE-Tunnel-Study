# 📁 시나리오 ① - Step 3. MGRE Tunnel + NHRP 구성

ISP-1, ISP-2, ISP-3 구간을 **MGRE** + **NHRP**로 연결합니다. (Tunnel은 1개만 사용)

## 🎯 조건

- Tunnel IP : **172.16.123.0/24** (Tunnel은 1개 이상 구성 불가)
- Tunnel Source : **Loopback 0** IP
- 서울 본사 (**ISP-1**) = NHRP Server (Hub)
- 나머지 = NHRP Client (Spoke)
- NHRP Network-ID = **123**, Authentication = **cisco**

---

## ISP-1 (R1) — Hub / NHRP Server

```cisco
interface tunnel 123
 ip address 172.16.123.1 255.255.255.0
 tunnel source 110.110.1.1
 tunnel mode gre multipoint
 ip nhrp network-id 123
 ip nhrp authentication cisco
 ip nhrp map multicast dynamic
!
end
```

## ISP-2 (R2) — Spoke / NHRP Client

```cisco
interface tunnel 123
 ip address 172.16.123.2 255.255.255.0
 tunnel source 110.110.2.2
 tunnel mode gre multipoint
 ip nhrp network-id 123
 ip nhrp authentication cisco
 ip nhrp nhs 172.16.123.1
 ip nhrp map multicast 110.110.1.1
 ip nhrp map 172.16.123.1 110.110.1.1
!
end
```

## ISP-3 (R3) — Spoke / NHRP Client

```cisco
interface tunnel 123
 ip address 172.16.123.3 255.255.255.0
 tunnel source 110.110.3.3
 tunnel mode gre multipoint
 ip nhrp network-id 123
 ip nhrp authentication cisco
 ip nhrp nhs 172.16.123.1
 ip nhrp map multicast 110.110.1.1
 ip nhrp map 172.16.123.1 110.110.1.1
!
end
```

---

## ✅ 검증

### ISP-1 (Hub)

```bash
ISP-1# show ip route connected
C    211.241.14.0/24 is directly connected, FastEthernet0/0
C    198.210.12.0/24 is directly connected, Serial1/0.12
     172.16.0.0/24 is subnetted, 1 subnets
C       172.16.123.0 is directly connected, Tunnel123    ← Tunnel 경로 확인
C    198.210.13.0/24 is directly connected, Serial1/0.13
     110.0.0.0/24 is subnetted, 4 subnets
C       110.110.1.0 is directly connected, Loopback0
C       110.110.10.0 is directly connected, Loopback1

ISP-1# show ip nhrp brief
Target            Via            NBMA          Mode      Intfc   Claimed
172.16.123.2/32   172.16.123.2   110.110.2.2   dynamic   Tu123   < >
172.16.123.3/32   172.16.123.3   110.110.3.3   dynamic   Tu123   < >

ISP-1# ping 172.16.123.2
Success rate is 100 percent (5/5), round-trip min/avg/max = 12/30/44 ms

ISP-1# ping 172.16.123.3
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/27/32 ms
```

### ISP-2 (Spoke)

```bash
ISP-2# show ip route connected
C    211.241.25.0/24 is directly connected, FastEthernet0/0
C    198.210.12.0/24 is directly connected, Serial1/0.12
     172.16.0.0/24 is subnetted, 1 subnets
C       172.16.123.0 is directly connected, Tunnel123    ← Tunnel 경로 확인
     110.0.0.0/24 is subnetted, 4 subnets
C       110.110.2.0 is directly connected, Loopback0
C       110.110.20.0 is directly connected, Loopback1
C    198.210.23.0/24 is directly connected, Serial1/0.23

ISP-2# show ip nhrp brief
Target            Via            NBMA          Mode     Intfc   Claimed
172.16.123.1/32   172.16.123.1   110.110.1.1   static   Tu123   < >

ISP-2# ping 172.16.123.1
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/25/32 ms

ISP-2# ping 172.16.123.3    ! Spoke ↔ Spoke 통신
Success rate is 100 percent (5/5), round-trip min/avg/max = 28/34/52 ms
```

### ISP-3 (Spoke)

```bash
ISP-3# show ip nhrp brief
Target            Via            NBMA          Mode     Intfc   Claimed
172.16.123.1/32   172.16.123.1   110.110.1.1   static   Tu123   < >

ISP-3# ping 172.16.123.1
Success rate is 100 percent (5/5), round-trip min/avg/max = 40/56/64 ms

ISP-3# ping 172.16.123.3
Success rate is 100 percent (5/5), round-trip min/avg/max = 52/60/64 ms
```
