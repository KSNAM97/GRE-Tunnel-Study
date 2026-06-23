# Chapter 3-2. Static Route - SOL-B (R2)

SOL-B에서 **SOL-A 사설망(192.168.10.0/24)** 으로 가는 경로를 Tunnel을 통해 설정합니다.

## 두 가지 표기법 (둘 중 하나 선택)

### 방법 1 — Next-hop IP 지정

```cisco
ip route 192.168.10.0 255.255.255.0 172.16.10.1
```

### 방법 2 — Outgoing Interface 지정

```cisco
ip route 192.168.10.0 255.255.255.0 tunnel 12
```

## ✅ 검증

```bash
SOL-B# show ip route
      172.16.0.0/24 is subnetted, 1 subnets
C        172.16.10.0 is directly connected, Tunnel12
S    192.168.10.0/24 [1/0] via 172.16.10.1                       ← SOL-A 내부 사설망
C    192.168.20.0/24 is directly connected, FastEthernet0/1      ← SOL-B 내부 사설망
~~~~~~~ 중간 생략 ~~~~~~~

PC2(R8)# ping 192.168.10.1
```
