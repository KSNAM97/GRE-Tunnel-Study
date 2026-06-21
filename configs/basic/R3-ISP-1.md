# R3 - ISP-1 기본 설정

공중망(ISP) 라우터 기본 구성입니다.

## 주요 정보

- **Hostname** : ISP-1
- **Loopback 0** : 100.100.11.1/24
- **S1/0.10** : 121.160.10.11/24 (DLCI 301)
- **S1/0.12** : 121.160.12.1/24 (DLCI 304)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname ISP-1
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
 ip address 100.100.11.1 255.255.255.0
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.10 point-to-point
 ip address 121.160.10.11 255.255.255.0
 frame-relay interface-dlci 301
!
interface serial 1/0.12 point-to-point
 ip address 121.160.12.1 255.255.255.0
 frame-relay interface-dlci 304
!
end
write memory
```
