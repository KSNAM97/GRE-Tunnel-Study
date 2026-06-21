# R6 - ISP-4 기본 설정

공중망(ISP) 라우터 기본 구성입니다.

## 주요 정보

- **Hostname** : ISP-4
- **Loopback 0** : 100.100.14.4/24
- **S1/0.34** : 121.160.34.4/24 (DLCI 605)
- **S1/0.20** : 121.160.20.4/24 (DLCI 602)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname ISP-4
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
 ip address 100.100.14.4 255.255.255.0
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.34 point-to-point
 ip address 121.160.34.4 255.255.255.0
 frame-relay interface-dlci 605
!
interface serial 1/0.20 point-to-point
 ip address 121.160.20.4 255.255.255.0
 frame-relay interface-dlci 602
!
end
write memory
```
