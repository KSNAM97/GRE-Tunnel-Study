# Chapter 2. NAT Overload - SOL-B (R2)

내부 192.168.20.0/24 네트워크를 **121.160.20.2** 하나의 IP로 PAT 변환합니다.

## 주요 설정 요소

| 항목 | 값 |
|------|-----|
| ACL | 20 (192.168.20.0/24) |
| NAT Pool 이름 | SOL-B |
| Pool 범위 | 121.160.20.2 ~ 121.160.20.2 (단일 IP) |
| Inside Interface | FastEthernet 0/0 |
| Outside Interface | Serial 1/0.20 |
| 방식 | overload (PAT) |

## Configuration

```cisco
access-list 20 permit 192.168.20.0 0.0.0.255
!
ip nat pool SOL-B 121.160.20.2 121.160.20.2 netmask 255.255.255.0
ip nat inside source list 20 pool SOL-B overload
!
interface fastethernet 0/0
 ip nat inside
!
interface serial 1/0.20
 ip nat outside
!
end
write memory
```
