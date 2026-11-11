# Lab 05. HSRP (First Hop Redundancy)

> 상태: 🔲 진행 전 | 도구: Cisco Packet Tracer (또는 GNS3 / EVE-NG)

## 목표
- 두 대의 게이트웨이를 하나의 가상 IP로 묶어 게이트웨이 이중화를 구현한다.
- Priority / Preempt로 Active 라우터를 지정한다.
- Interface Tracking으로 업링크 장애 시 자동 전환되게 한다.
- 장애 발생 시 전환(Failover) 과정을 관찰한다.

## 토폴로지

```
                 [Upstream / ISP]
                  /            \
            g0/1 /              \ g0/1
             [R1]                [R2]
          Active (110)       Standby (100)
             g0/0 \            / g0/0
                   \          /
                      [SW1]
                     /     \
                   PC1     PC2
          Gateway = 192.168.10.254 (Virtual IP)
```

## 주소 체계

| 장비 | IP |
|---|---|
| R1 g0/0 | 192.168.10.1/24 |
| R2 g0/0 | 192.168.10.2/24 |
| HSRP Virtual IP | 192.168.10.254 |
| PC 게이트웨이 | 192.168.10.254 |

## 핵심 설정

```
! R1 (Active)
track 1 interface g0/1 line-protocol
!
interface g0/0
 ip address 192.168.10.1 255.255.255.0
 standby version 2
 standby 10 ip 192.168.10.254
 standby 10 priority 110
 standby 10 preempt
 standby 10 track 1 decrement 20
!
! R2 (Standby)
interface g0/0
 ip address 192.168.10.2 255.255.255.0
 standby version 2
 standby 10 ip 192.168.10.254
 standby 10 preempt
```

## 검증 체크리스트
- [ ] `show standby brief`: R1 = Active, R2 = Standby
- [ ] PC에서 `arp -a`: 게이트웨이 MAC이 HSRP 가상 MAC(0000.0c9f.fXXX)인지 확인
- [ ] PC1에서 `ping -t` 실행 중 R1 g0/0 shutdown → 손실된 패킷 수 기록
- [ ] R1 g0/1(업링크) shutdown → Tracking으로 R2가 Active가 되는지 확인
- [ ] R1 복구 후 Preempt로 Active를 되찾는지 확인

## 결과
<!-- 장애 전후 show standby brief 출력, ping 손실 수를 붙여 넣으세요 -->

## 트러블슈팅 / 배운 점
<!-- 예: preempt 미설정 시 동작, HSRP v1/v2 불일치, 그룹 번호 불일치 -->
