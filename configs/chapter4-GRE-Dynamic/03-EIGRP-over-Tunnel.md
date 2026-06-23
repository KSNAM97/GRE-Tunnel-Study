# Chapter 4-3. Tunnel 위 EIGRP 활성화 (SOL-A & SOL-B)

생성된 Tunnel을 EIGRP에 포함시켜 사설망을 **동적으로 학습**합니다.

## SOL-A (R1) Configuration

```cisco
router eigrp 100
 no passive-interface tunnel 12
 network 172.16.100.0 0.0.0.255
!
end
write memory
```

## SOL-B (R2) Configuration

```cisco
router eigrp 100
 no passive-interface tunnel 12
 network 172.16.100.0 0.0.0.255
!
end
write memory
```

## ✅ EIGRP Neighbor 형성 + Route 학습

```bash
GIT-A# show ip route eigrp
     100.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
D       100.100.20.0/24 [90/297372416] via 172.16.100.2, 00:00:21, Tunnel12
D    192.168.20.0/24 [90/297270016] via 172.16.100.2, 00:00:21, Tunnel12

GIT-B# show ip route eigrp
     100.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
D       100.100.10.0/24 [90/297372416] via 172.16.100.1, 00:00:06, Tunnel12
D    192.168.10.0/24 [90/297270016] via 172.16.100.1, 00:00:06, Tunnel12
```

## ✅ End-to-End Ping/Traceroute

```bash
PC1# ping 192.168.20.2
Success rate is 100 percent (5/5), round-trip min/avg/max = 264/283/332 ms

PC2# ping 192.168.10.1
Success rate is 100 percent (5/5), round-trip min/avg/max = 236/281/316 ms

PC1# traceroute 130.30.3.3
  1 192.168.10.254
  2 121.160.10.11
  3 121.160.12.2
  4 121.160.23.3

PC2# traceroute 120.20.2.2
  1 192.168.20.254
  2 121.160.20.4
  3 121.160.34.3
  4 121.160.23.2
```
