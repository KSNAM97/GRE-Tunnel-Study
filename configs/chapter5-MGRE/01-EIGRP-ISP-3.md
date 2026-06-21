# Chapter 5-1. 공중망 EIGRP - ISP-3 (R3)

ISP 공중망을 **EIGRP AS 2020**으로 구성합니다.

## 적용 인터페이스

- Loopback 0 (110.110.3.0/24)
- Serial 1/0.13 (198.210.13.0/24)
- Serial 1/0.23 (198.210.23.0/24)
- FastEthernet 0/0 (211.241.36.0/24) — GIT-B 연결

## Configuration

```cisco
router eigrp 2020
 no auto-summary
 passive-interface default
 no passive-interface serial 1/0.13
 no passive-interface serial 1/0.23
 network 110.110.3.0   0.0.0.255
 network 198.210.13.0  0.0.0.255
 network 198.210.23.0  0.0.0.255
 network 211.241.36.0  0.0.0.255
!
end
write memory
```

## ✅ 검증

```bash
ISP-1# show ip eigrp neighbors   ! 인접성 2개
ISP-2# show ip eigrp neighbors   ! 인접성 2개
ISP-3# show ip eigrp neighbors   ! 인접성 2개
```
