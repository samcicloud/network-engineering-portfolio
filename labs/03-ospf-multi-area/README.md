# Lab 03. OSPF Single / Multi-Area

> 상태: 🔲 진행 전 | 도구: Cisco Packet Tracer (또는 GNS3 / EVE-NG)

## 목표
- OSPF 인접 관계(Neighbor/Adjacency) 형성 과정을 이해한다.
- Single-Area로 시작해 Multi-Area(Area 0 + Area 1)로 확장한다.
- ABR의 역할과 LSA Type 1/2/3의 차이를 확인한다.
- 경로 요약과 Passive Interface를 적용한다.

## 토폴로지

```
   Area 1                Area 0                 Area 0
 [LAN1]--[R1] ------- [R2] (ABR) ------- [R3] ------- [R4]--[LAN4]
          10.0.12.0/30      10.0.23.0/30      10.0.34.0/30
```

## 주소 체계

| 링크 / 네트워크 | 서브넷 | Area |
|---|---|---|
| R1 LAN | 172.16.1.0/24 | 1 |
| R1 - R2 | 10.0.12.0/30 | 1 |
| R2 - R3 | 10.0.23.0/30 | 0 |
| R3 - R4 | 10.0.34.0/30 | 0 |
| R4 LAN | 172.16.4.0/24 | 0 |
| Router ID | R1=1.1.1.1, R2=2.2.2.2, R3=3.3.3.3, R4=4.4.4.4 | |

## 핵심 설정

```
router ospf 1
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 1
 network 10.0.23.0 0.0.0.3 area 0
 passive-interface g0/2        ! LAN 쪽 인터페이스 (R1/R4)
!
! ABR(R2)에서 Area 1 경로 요약
router ospf 1
 area 1 range 172.16.0.0 255.255.0.0
```

## 검증 체크리스트
- [ ] `show ip ospf neighbor`: 모든 이웃이 `FULL` 상태
- [ ] `show ip route ospf`: `O`(intra-area), `O IA`(inter-area) 경로 구분
- [ ] `show ip ospf database`: LSA Type 1, 2, 3 확인
- [ ] `show ip protocols`: Router ID, Passive Interface 확인
- [ ] LAN1 → LAN4 ping / traceroute 성공

## 결과
<!-- show 명령어 출력을 붙여 넣으세요 -->

## 장애 재현 실습 (추천)
일부러 아래 문제를 만들고 `show` / `debug ip ospf adj`로 원인을 찾아보세요.
- [ ] 양쪽 Area ID 불일치
- [ ] Hello / Dead 타이머 불일치
- [ ] MTU 불일치 (EXSTART/EXCHANGE에서 멈춤)
- [ ] Router ID 중복

## 트러블슈팅 / 배운 점
<!-- 문제 → 확인한 명령어 → 원인 → 해결 순서로 정리 -->
