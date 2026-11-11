# Lab 01. VLAN & Inter-VLAN Routing

> 상태: 🔲 진행 전 | 도구: Cisco Packet Tracer (또는 GNS3 / EVE-NG)

## 목표
- VLAN으로 브로드캐스트 도메인을 분리한다.
- Trunk(802.1Q)로 여러 VLAN을 한 링크에 전달한다.
- 두 가지 방식으로 VLAN 간 통신을 구현하고 비교한다.
  - A. Router-on-a-stick (서브인터페이스)
  - B. L3 스위치 SVI

## 토폴로지

```
            [R1]  (방식 A에서만 사용)
             | G0/0 (trunk)
             |
            [SW1] ---- trunk ---- [SW2]
            /    \               /    \
         PC1     PC2          PC3     PC4
       VLAN10  VLAN20       VLAN10  VLAN20
```

> 실습 후 구성도 이미지를 `topology.png`로 저장해 교체하세요.

## 주소 체계

| VLAN | 이름 | 서브넷 | 게이트웨이 |
|---|---|---|---|
| 10 | SALES | 192.168.10.0/24 | 192.168.10.1 |
| 20 | ENG | 192.168.20.0/24 | 192.168.20.1 |
| 99 | MGMT | 192.168.99.0/24 | 192.168.99.1 |

| 장비 | IP |
|---|---|
| PC1 / PC3 | 192.168.10.11 / 192.168.10.13 |
| PC2 / PC4 | 192.168.20.12 / 192.168.20.14 |

## 핵심 설정

### 스위치 (VLAN, Access, Trunk)
```
vlan 10
 name SALES
vlan 20
 name ENG
!
interface f0/1
 switchport mode access
 switchport access vlan 10
!
interface g0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,99
```

### 방식 A: Router-on-a-stick
```
interface g0/0
 no shutdown
interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

### 방식 B: L3 스위치 SVI
```
ip routing
interface vlan 10
 ip address 192.168.10.1 255.255.255.0
interface vlan 20
 ip address 192.168.20.1 255.255.255.0
```

## 검증 체크리스트
- [ ] `show vlan brief`: 포트가 올바른 VLAN에 할당됨
- [ ] `show interfaces trunk`: trunk 상태 및 허용 VLAN 확인
- [ ] 같은 VLAN 통신: PC1 → PC3 ping 성공
- [ ] 다른 VLAN 통신: PC1 → PC2 ping 성공 (라우팅 후)
- [ ] `show ip route`: 연결된 서브넷(C) 확인

## 결과
<!-- show 명령어 출력, ping 결과 스크린샷/텍스트를 붙여 넣으세요 -->

## 방식 A vs B 비교
| 항목 | Router-on-a-stick | L3 스위치 SVI |
|---|---|---|
| 성능 | <!-- 직접 정리 --> | |
| 확장성 | | |
| 비용 / 구성 난이도 | | |

## 트러블슈팅 / 배운 점
<!-- 예: native VLAN 불일치, allowed VLAN 누락 등 겪은 문제와 해결 과정 -->
