# Chapter 4-1. EIGRP 사설망 - SOL-A (R1)

SOL-A 사설망에 **EIGRP AS 100**을 설정합니다. (Tunnel 미포함 단계)

## 적용 인터페이스

- 192.168.10.0 (F0/0 - 사설망)
- 100.100.10.0/24 (Loopback 1)

## Configuration

```cisco
router eigrp 100
 no auto-summary
 passive-interface default
 network 192.168.10.0
 network 100.100.10.0 0.0.0.255
!
end
write memory
```

> ⚠️ 이 시점에는 Neighbor가 형성되지 않습니다. Tunnel을 생성한 후 활성화됩니다.
