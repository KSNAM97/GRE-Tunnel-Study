# R1 - SOL-A (GIT-A) 기본 설정

본사 Gateway 라우터의 기본 구성입니다.

## 주요 정보

- **Hostname** : GIT-A
- **Loopback 0** : 100.100.1.1/24
- **Loopback 1** : 100.100.10.1/24
- **F0/0** : 192.168.10.254/24 (내부)
- **S1/0.10** : 121.160.10.1/24 (DLCI 103)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname GIT-A
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
interface loopback 0
 ip address 100.100.1.1 255.255.255.0
!
interface loopback 1
 ip address 100.100.10.1 255.255.255.0
!
interface fastethernet 0/0
 ip address 192.168.10.254 255.255.255.0
 no shutdown
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.10 point-to-point
 ip address 121.160.10.1 255.255.255.0
 frame-relay interface-dlci 103
!
end
write memory
```
