# Chapter 3-1. GRE Tunnel 생성 - SOL-B (R2)

물리 인터페이스 IP를 사용하여 SOL-A와 GRE Tunnel을 형성합니다.

## 주요 설정 요소

| 항목 | 값 |
|------|-----|
| Tunnel Interface | Tunnel 12 |
| Tunnel IP | 172.16.10.2/24 |
| Tunnel Source | 121.160.20.2 (Serial 1/0.20) |
| Tunnel Destination | 121.160.10.1 (SOL-A Serial 1/0.10) |

## Configuration

```cisco
interface tunnel 12
 ip address 172.16.10.2 255.255.255.0
 tunnel source 121.160.20.2
 tunnel destination 121.160.10.1
!
end
write memory
```

## ✅ 검증

```bash
SOL-B# show ip route connected
      100.0.0.0/24 is subnetted, 7 subnets
C        100.100.2.0 is directly connected, Loopback0
C        100.100.20.0 is directly connected, Loopback1
      172.16.0.0/24 is subnetted, 1 subnets
C        172.16.10.0 is directly connected, Tunnel12     ← Tunnel 경로 확인
C    192.168.20.0/24 is directly connected, FastEthernet0/0
      121.0.0.0/24 is subnetted, 5 subnets
C        121.160.20.0 is directly connected, Serial1/0.20

SOL-B# ping 172.16.10.1
```
