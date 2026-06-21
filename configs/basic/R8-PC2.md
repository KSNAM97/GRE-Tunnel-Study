# R8 - PC2 (SOL-B 내부 PC)

SOL-B 내부 네트워크의 PC 역할 라우터입니다.

## 주요 정보

- **Hostname** : PC2
- **F0/0** : 192.168.20.2/24
- **Default Gateway** : 192.168.20.254 (SOL-B)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname PC2
!
line con 0
 logging syn
 exec-timeout 0 0
 login
!
line vty 0 4
 password cisco
 login
!
interface fastethernet 0/0
 ip address 192.168.20.2 255.255.255.0
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 192.168.20.254
!
end
write memory
```
