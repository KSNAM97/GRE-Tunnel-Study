# 📁 Chapter 4. GRE Tunnel (Dynamic) + EIGRP 구성

SOL-A와 SOL-B 간을 **GRE Tunnel**로 연결하고, 사설망 라우팅을 **EIGRP**로 동적으로 학습합니다.

## 🎯 조건

- Tunnel Network : **172.16.100.0/24**
- Tunnel Source / Destination : **Loopback 0** IP 사용
  - SOL-A : 100.100.1.1
  - SOL-B : 100.100.2.2
- EIGRP AS = 100
- `no auto-summary` 적용
- 사설망 + Loopback 1 + Tunnel은 EIGRP 포함
- `passive-interface default` 사용

## 파일 목록

| 파일 | 장비 |
|------|------|
| [SOL-A.md](./SOL-A.md) | R1 |
| [SOL-B.md](./SOL-B.md) | R2 |

## ✅ 검증

```bash
GIT-A# show ip route eigrp
D 100.100.20.0/24 [90/297372416] via 172.16.100.2, Tunnel12
D 192.168.20.0/24 [90/297270016] via 172.16.100.2, Tunnel12

GIT-B# show ip route eigrp
D 100.100.10.0/24 [90/297372416] via 172.16.100.1, Tunnel12
D 192.168.10.0/24 [90/297270016] via 172.16.100.1, Tunnel12

PC1# ping 192.168.20.2
PC2# ping 192.168.10.1
```

## 🔍 Traceroute 결과

```bash
PC1# traceroute 130.30.3.3
1  192.168.10.254     64 ms
2  121.160.10.11     124 ms
3  121.160.12.2      148 ms
4  121.160.23.3      184 ms

PC2# traceroute 120.20.2.2
1  192.168.20.254     68 ms
2  121.160.20.4       92 ms
3  121.160.34.3      128 ms
4  121.160.23.2      184 ms
```
