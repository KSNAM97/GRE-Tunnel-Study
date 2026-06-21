# Chapter 3. GRE Tunnel (Static) - SOL-B (R2)

물리 인터페이스 IP를 사용하여 SOL-A와 GRE Tunnel을 형성합니다.

## 주요 설정 요소

- Tunnel 12 : 172.16.10.2/24
- Source : 121.160.20.2 (S1/0.20)
- Destination : 121.160.10.1 (SOL-A S1/0.10)
- Static Route : 192.168.10.0/24 → 172.16.10.1

## Configuration

```cisco
interface tunnel 12
 ip address 172.16.10.2 255.255.255.0
 tunnel source 121.160.20.2
 tunnel destination 121.160.10.1
!
! 사설망 라우팅을 위한 Static Route
ip route 192.168.10.0 255.255.255.0 172.16.10.1
!
! (또는 next-hop 대신 outgoing interface 사용)
! ip route 192.168.10.0 255.255.255.0 tunnel 12
!
end
write memory
```
