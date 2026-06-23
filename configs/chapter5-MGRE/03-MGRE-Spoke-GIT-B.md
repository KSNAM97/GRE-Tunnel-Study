# 📁 시나리오 ② - Step 3. MGRE Tunnel + NHRP 구성

GIT-HQ, GIT-A, GIT-B 구간을 **MGRE** + **NHRP**로 연결합니다.

## 🎯 조건

- Tunnel IP : **172.16.100.0/24**
- Tunnel Source : **FastEthernet 0/0** IP
- 서울 본사 (**GIT-HQ**) = NHRP Server (Hub)
- 나머지 = NHRP Client (Spoke)
- NHRP Network-ID = **619**, Authentication = **soldesk**

---

## GIT-HQ (R4) — Hub / NHRP Server

```cisco
interface tunnel 123
 ip address 172.16.100.1 255.255.255.0
 tunnel source 211.241.14.1
 tunnel mode gre multipoint
 ip nhrp network-id 619
 ip nhrp authentication soldesk
 ip nhrp map multicast dynamic
!
end
```

## GIT-A (R5) — Spoke / NHRP Client

```cisco
interface tunnel 123
 ip address 172.16.100.2 255.255.255.0
 tunnel source 211.241.25.2
 tunnel mode gre multipoint
 ip nhrp network-id 619
 ip nhrp authentication soldesk
 ip nhrp nhs 211.241.14.1
 ip nhrp map multicast 220.220.1.1
 ip nhrp map 172.16.100.1 211.241.14.1
!
end
```

## GIT-B (R6) — Spoke / NHRP Client

```cisco
interface tunnel 123
 ip address 172.16.100.3 255.255.255.0
 tunnel source 211.241.36.3
 tunnel mode gre multipoint
 ip nhrp network-id 619
 ip nhrp authentication soldesk
 ip nhrp nhs 211.241.14.1
 ip nhrp map multicast 220.220.1.1
 ip nhrp map 172.16.100.1 211.241.14.1
!
end
```

---

## ✅ 검증 — NHRP Dynamic Registration

```bash
GIT-HQ# show ip nhrp dynamic
172.16.100.2/32 via 172.16.100.2, Tunnel123 created 00:03:12, expire 01:59:31
  Type: dynamic, Flags: authoritative unique registered used
  NBMA address: 211.241.25.2
172.16.100.3/32 via 172.16.100.3, Tunnel123 created 00:00:18, expire 01:59:41
  Type: dynamic, Flags: authoritative unique registered used
  NBMA address: 211.241.36.3

GIT-A# show ip nhrp dynamic
172.16.100.3/32 via 172.16.100.3, Tunnel123 created 00:00:03, expire 01:59:07
  Type: dynamic, Flags: router used
  NBMA address: 211.241.36.3

GIT-B# show ip nhrp dynamic
172.16.100.2/32 via 172.16.100.2, Tunnel123 created 00:00:37, expire 01:58:23
  Type: dynamic, Flags: router used
  NBMA address: 211.241.25.2
```
