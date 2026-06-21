# R2 - SOL-B (GIT-B) 기본 설정

지사 Gateway 라우터의 기본 구성입니다.

## 주요 정보

- **Hostname** : GIT-B
- **Loopback 0** : 100.100.2.2/24
- **Loopback 1** : 100.100.20.2/24
- **F0/0** : 192.168.20.254/24 (내부)
- **S1/0.20** : 121.160.20.2/24 (DLCI 206)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname GIT-B
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
 ip address 100.100.2.2 255.255.255.0
!
interface loopback 1
 ip address 100.100.20.2 255.255.255.0
!
interface fastethernet 0/0
 ip address 192.168.20.254 255.255.255.0
 no shutdown
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.20 point-to-point
 ip address 121.160.20.2 255.255.255.0
 frame-relay interface-dlci 206
!
end
write memory
```
