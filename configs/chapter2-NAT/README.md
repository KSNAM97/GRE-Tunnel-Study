# 📁 Chapter 2. NAT Overload (PAT)

SOL-A, SOL-B의 내부 사설망이 외부 통신 시 **공인 IP (Serial 인터페이스 IP)** 하나로 변환되도록 PAT를 구성합니다.

## 🎯 조건

- SOL-A : 192.168.10.0/24 → Serial1/0.10 IP (121.160.10.1) 로 PAT
- SOL-B : 192.168.20.0/24 → Serial1/0.20 IP (121.160.20.2) 로 PAT

## 파일 목록

| 파일 | 장비 |
|------|------|
| [SOL-A.md](./SOL-A.md) | R1 |
| [SOL-B.md](./SOL-B.md) | R2 |

## ✅ 검증

### SOL-A

```bash
PC1(R7)# ping 120.20.2.2
PC1(R7)# ping 130.30.3.3

SOL-A# show ip nat translation
Pro Inside global       Inside local        Outside local       Outside global
icmp 121.160.10.1:0    192.168.10.1:0      120.20.2.2:0        120.20.2.2:0
icmp 121.160.10.1:1    192.168.10.1:1      130.30.3.3:1        130.30.3.3:1
```

### SOL-B

```bash
SOL-B# ping 120.20.2.2
SOL-B# ping 130.30.3.3

SOL-B# show ip nat translation
Pro Inside global       Inside local        Outside local       Outside global
icmp 121.160.20.2:1    192.168.20.2:1      120.20.2.2:1        120.20.2.2:1
icmp 121.160.20.2:2    192.168.20.2:2      130.30.3.3:2        130.30.3.3:2
```
