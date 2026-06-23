# 📁 시나리오 ② - Step 1. 공중망 EIGRP 구성

ISP-1, ISP-2, ISP-3 구간을 **EIGRP**로 공중망 구성합니다.

## 🎯 조건

- EIGRP Process = **2020**, `no auto-summary`
- 필요한 Interface로만 EIGRP Packet 송신
- Serial, Loopback 0, F0/0 네트워크 모두 EIGRP 포함

---

## ISP-1 (R1)

```cisco
router eigrp 2020
 no auto-summary
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.13
 network 110.110.1.0   0.0.0.255
 network 198.210.12.0  0.0.0.255
 network 198.210.13.0  0.0.0.255
 network 211.241.14.0  0.0.0.255
!
end
```

## ISP-2 (R2)

```cisco
router eigrp 2020
 no auto-summary
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.23
 network 110.110.2.0   0.0.0.255
 network 198.210.12.0  0.0.0.255
 network 198.210.23.0  0.0.0.255
 network 211.241.25.0  0.0.0.255
!
end
```

## ISP-3 (R3)

```cisco
router eigrp 2020
 no auto-summary
 passive-interface default
 no passive-interface serial 1/0.13
 no passive-interface serial 1/0.23
 network 110.110.3.0   0.0.0.255
 network 198.210.13.0  0.0.0.255
 network 198.210.23.0  0.0.0.255
 network 211.241.36.0  0.0.0.255
!
end
```

## ✅ 검증

```bash
ISP-1# show ip eigrp neighbors
H  Address         Interface   Hold  Uptime    SRTT  RTO  Q  Seq
1  198.210.13.3    Se1/0.13    14    00:00:31  44    264  0  9
0  198.210.12.2    Se1/0.12    13    00:00:42  40    240  0  8

ISP-2# show ip eigrp neighbors
H  Address         Interface   Hold  Uptime    SRTT  RTO  Q  Seq
1  198.210.23.3    Se1/0.23    14    00:00:25  48    288  0  10
0  198.210.12.1    Se1/0.12    14    00:00:37  50    300  0  8

ISP-3# show ip eigrp neighbors
H  Address         Interface   Hold  Uptime    SRTT  RTO   Q  Seq
1  198.210.23.2    Se1/0.23    11    00:00:18  850   5000  0  9
0  198.210.13.1    Se1/0.13    10    00:00:18  849   5000  0  9
```
