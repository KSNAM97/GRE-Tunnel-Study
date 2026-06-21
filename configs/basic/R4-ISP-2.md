# R4 - ISP-2 기본 설정

공중망(ISP) 라우터 기본 구성입니다.

## 주요 정보

- **Hostname** : ISP-2
- **Loopback 0** : 100.100.12.2/24
- **Loopback 120** : 120.20.2.2/24
- **S1/0.12** : 121.160.12.2/24 (DLCI 403)
- **S1/0.23** : 121.160.23.2/24 (DLCI 405)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname ISP-2
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
 ip address 100.100.12.2 255.255.255.0
!
interface loopback 120
 ip address 120.20.2.2 255.255.255.0
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.12 point-to-point
 ip address 121.160.12.2 255.255.255.0
 frame-relay interface-dlci 403
!
interface serial 1/0.23 point-to-point
 ip address 121.160.23.2 255.255.255.0
 frame-relay interface-dlci 405
!
end
write memory
```
