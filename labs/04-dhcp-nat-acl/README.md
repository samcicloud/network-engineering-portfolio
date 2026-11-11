# Lab 04. DHCP, NAT/PAT, ACL

> 상태: 🔲 진행 전 | 도구: Cisco Packet Tracer (또는 GNS3 / EVE-NG)

## 목표
- 라우터를 DHCP 서버로 구성하고, 다른 서브넷 클라이언트를 위해 DHCP Relay를 설정한다.
- PAT(NAT Overload)로 내부 사설망이 인터넷에 접속하도록 한다.
- Static NAT로 내부 서버를 외부에 공개한다.
- Standard / Extended ACL로 트래픽을 제어한다.

## 토폴로지

```
 [PC VLAN10]--+                                    
              |-- [SW1] --- [R1 (DHCP/NAT)] --- [ISP] --- [Internet Server]
 [PC VLAN20]--+       inside         outside      203.0.113.0/29   198.51.100.10
 [Web Server 192.168.10.100]
```

## 주소 체계

| 구간 | 서브넷 |
|---|---|
| Inside VLAN10 | 192.168.10.0/24 |
| Inside VLAN20 | 192.168.20.0/24 |
| R1 - ISP (Outside) | 203.0.113.0/29 (ISP = .1, R1 = .2, Web Server Static NAT = .3) |
| 외부 테스트 서버 | 198.51.100.10 |

## 핵심 설정

### DHCP
```
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
!
! 다른 장비가 게이트웨이일 때 (DHCP Relay)
interface g0/0.20
 ip helper-address <DHCP 서버 IP>
```

### PAT / Static NAT
```
access-list 1 permit 192.168.0.0 0.0.255.255
ip nat inside source list 1 interface g0/1 overload
ip nat inside source static 192.168.10.100 203.0.113.3
!
interface g0/0
 ip nat inside
interface g0/1
 ip nat outside
```

### Extended ACL (예: VLAN20 → Web Server는 HTTP만 허용)
```
ip access-list extended VLAN20-POLICY
 permit tcp 192.168.20.0 0.0.0.255 host 192.168.10.100 eq 80
 deny   ip 192.168.20.0 0.0.0.255 host 192.168.10.100
 permit ip any any
!
interface g0/0.20
 ip access-group VLAN20-POLICY in
```

## 검증 체크리스트
- [ ] PC에서 `ipconfig /renew` 후 IP 자동 할당 확인, `show ip dhcp binding`
- [ ] `show ip nat translations`: 내부 → 외부 변환 테이블 확인
- [ ] `show ip nat statistics`: 변환 hit 수 확인
- [ ] `show access-lists`: ACL 항목별 match 카운트 확인
- [ ] VLAN20 PC → Web Server: HTTP 허용 / ping 차단 확인

## 결과
<!-- show 명령어 출력을 붙여 넣으세요 -->

## 트러블슈팅 / 배운 점
<!-- 예: inside/outside 방향 반대 설정, ACL 순서와 암묵적 deny, wildcard mask 실수 -->
