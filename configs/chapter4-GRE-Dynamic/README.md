# 📁 Chapter 4. GRE Tunnel (Dynamic) + EIGRP — Loopback 기반

SOL-A와 SOL-B 간을 **GRE Tunnel**로 연결하고, 사설망 라우팅을 **EIGRP**로 동적으로 학습합니다.  
Chapter 3과 달리 Tunnel Source/Destination을 **Loopback 0**으로 사용합니다.

## 🎯 조건

- Tunnel Network : **172.16.100.0/24**
- Tunnel Source / Destination : **Loopback 0** IP 사용
  - SOL-A : 100.100.1.1
  - SOL-B : 100.100.2.2
- EIGRP AS = 100
- `no auto-summary` 적용
- 사설망 + Loopback 1 + Tunnel은 EIGRP 포함
- `passive-interface default` 사용

## 📂 파일 목록

| 파일 | 단계 |
|------|------|
| [01-EIGRP-Private-SOL-A.md](./01-EIGRP-Private-SOL-A.md) | SOL-A EIGRP 사설망 |
| [01-EIGRP-Private-SOL-B.md](./01-EIGRP-Private-SOL-B.md) | SOL-B EIGRP 사설망 |
| [02-Tunnel-SOL-A.md](./02-Tunnel-SOL-A.md) | SOL-A Tunnel 생성 (Loopback Source) |
| [02-Tunnel-SOL-B.md](./02-Tunnel-SOL-B.md) | SOL-B Tunnel 생성 (Loopback Source) |
| [03-EIGRP-over-Tunnel.md](./03-EIGRP-over-Tunnel.md) | Tunnel 위 EIGRP 활성화 |

## ✅ 최종 검증

### EIGRP 학습 라우트

```bash
GIT-A# show ip route eigrp
     100.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
D       100.100.20.0/24 [90/297372416] via 172.16.100.2, 00:00:21, Tunnel12
D    192.168.20.0/24 [90/297270016] via 172.16.100.2, 00:00:21, Tunnel12

GIT-B# show ip route eigrp
     100.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
D       100.100.10.0/24 [90/297372416] via 172.16.100.1, 00:00:06, Tunnel12
D    192.168.10.0/24 [90/297270016] via 172.16.100.1, 00:00:06, Tunnel12
```

### 사설망 End-to-End 통신

```bash
PC1# ping 192.168.20.2
Success rate is 100 percent (5/5), round-trip min/avg/max = 264/283/332 ms

PC2# ping 192.168.10.1
Success rate is 100 percent (5/5), round-trip min/avg/max = 236/281/316 ms
```

### Traceroute로 경로 추적

```bash
PC1# traceroute 130.30.3.3
  1 192.168.10.254 64 msec 60 msec 64 msec
  2 121.160.10.11 124 msec 120 msec 120 msec
  3 121.160.12.2 148 msec 120 msec 148 msec
  4 121.160.23.3 184 msec 184 msec 180 msec

PC2# traceroute 120.20.2.2
  1 192.168.20.254 68 msec 64 msec 60 msec
  2 121.160.20.4 92 msec 92 msec 88 msec
  3 121.160.34.3 128 msec 120 msec 148 msec
  4 121.160.23.2 184 msec 192 msec 148 msec
```
