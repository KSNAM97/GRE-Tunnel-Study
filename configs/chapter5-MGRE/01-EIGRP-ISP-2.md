# Chapter 5-1. 공중망 EIGRP - ISP-2 (R2)

ISP 공중망을 **EIGRP AS 2020**으로 구성합니다.

## 적용 인터페이스

- Loopback 0 (110.110.2.0/24)
- Serial 1/0.12 (198.210.12.0/24)
- Serial 1/0.23 (198.210.23.0/24)
- FastEthernet 0/0 (211.241.25.0/24) — GIT-A 연결

## Configuration

```cisco
router eigrp 2020
 no auto-summary
 passive-interface default
 no passive-interface serial 1/0.12
 no passive-interface serial 1/0.23
 network 110.110.2.0   0.0.0.255
 network 198.210.12.0  0.0.0.255
 network 198.210.23.0  0.0.0.255
 network 211.241.25.0  0.0.0.255
!
end
write memory
```
