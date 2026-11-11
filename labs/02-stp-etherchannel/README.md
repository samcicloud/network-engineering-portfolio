# Lab 02. STP & EtherChannel

> 상태: 🔲 진행 전 | 도구: Cisco Packet Tracer (또는 GNS3 / EVE-NG)

## 목표
- 이중화된 L2 구조에서 STP가 루프를 막는 과정을 확인한다.
- Root Bridge를 의도한 스위치로 지정한다.
- PortFast / BPDU Guard로 엣지 포트를 보호한다.
- LACP EtherChannel로 링크를 묶어 대역폭과 이중화를 확보한다.

## 토폴로지

```
              [SW1]  (Root 지정)
             //    \\
   Po1(LACP)//      \\Po2(LACP)
           //        \\
        [SW2] ------ [SW3]
          |            |
         PC1          PC2
```

## 핵심 설정

### Rapid PVST+ 및 Root Bridge 지정
```
spanning-tree mode rapid-pvst
! SW1
spanning-tree vlan 10,20 root primary
! SW2
spanning-tree vlan 10,20 root secondary
```

### 엣지 포트 보호
```
interface f0/1
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### LACP EtherChannel
```
interface range g0/1 - 2
 channel-group 1 mode active
!
interface port-channel 1
 switchport mode trunk
```

## 검증 체크리스트
- [ ] `show spanning-tree`: Root Bridge, 포트 역할(Root/Designated/Alternate) 확인
- [ ] `show etherchannel summary`: Po1, Po2가 `SU` / 멤버 포트 `P` 상태
- [ ] Root 쪽 링크를 끊었을 때 Alternate 포트가 Forwarding으로 바뀌는 시간 관찰
- [ ] PortFast 포트에 스위치를 연결했을 때 BPDU Guard로 err-disabled 되는지 확인

## 결과
<!-- show 명령어 출력, 장애 전후 비교를 붙여 넣으세요 -->

## 트러블슈팅 / 배운 점
<!-- 예: EtherChannel 양쪽 설정 불일치(speed/duplex/trunk), err-disabled 복구 방법 -->
