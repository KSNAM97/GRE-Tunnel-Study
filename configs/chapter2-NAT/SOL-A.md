# Chapter 2. NAT Overload - SOL-A (R1)

내부 192.168.10.0/24 네트워크를 **121.160.10.1** 하나의 IP로 PAT 변환합니다.

## 주요 설정 요소

| 항목 | 값 |
|------|-----|
| ACL | 10 (192.168.10.0/24) |
| NAT Pool 이름 | SOL-A |
| Pool 범위 | 121.160.10.1 ~ 121.160.10.1 (단일 IP) |
| Inside Interface | FastEthernet 0/0 |
| Outside Interface | Serial 1/0.10 |
| 방식 | overload (PAT) |

## Configuration

```cisco
access-list 10 permit 192.168.10.0 0.0.0.255
!
ip nat pool SOL-A 121.160.10.1 121.160.10.1 netmask 255.255.255.0
ip nat inside source list 10 pool SOL-A overload
!
interface fastethernet 0/0
 ip nat inside
!
interface serial 1/0.10
 ip nat outside
!
end
write memory
```
