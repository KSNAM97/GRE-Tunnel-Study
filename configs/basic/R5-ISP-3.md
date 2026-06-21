# R5 - ISP-3 기본 설정

공중망(ISP) 라우터 기본 구성입니다.

## 주요 정보

- **Hostname** : ISP-3
- **Loopback 0** : 100.100.13.3/24
- **Loopback 130** : 130.30.3.3/24
- **S1/0.23** : 121.160.23.3/24 (DLCI 504)
- **S1/0.34** : 121.160.34.3/24 (DLCI 506)

## Configuration

```cisco
en
conf t
!
no ip domain-lookup
!
hostname ISP-3
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
 ip address 100.100.13.3 255.255.255.0
!
interface loopback 130
 ip address 130.30.3.3 255.255.255.0
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.23 point-to-point
 ip address 121.160.23.3 255.255.255.0
 frame-relay interface-dlci 504
!
interface serial 1/0.34 point-to-point
 ip address 121.160.34.3 255.255.255.0
 frame-relay interface-dlci 506
!
end
write memory
```
