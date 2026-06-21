# R7 - PC1 (SOL-A 내부 PC)

SOL-A 내부 네트워크의 PC 역할 라우터입니다.

## 주요 정보

- **Hostname** : PC1
- **F0/0** : 192.168.10.1/24
- **Default Gateway** : 192.168.10.254 (SOL-A)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname PC1
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
 ip address 192.168.10.1 255.255.255.0
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 192.168.10.254
!
end
write memory
```
