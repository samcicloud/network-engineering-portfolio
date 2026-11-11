# Labs

토폴로지 기반 실습 랩을 모아둡니다. 랩 하나당 폴더 하나를 만들어 주세요.

```
labs/
└── 01-ospf-multi-area/
    ├── README.md        # 목표, 구성도, 설정 요약, 검증 결과
    ├── topology.png     # 네트워크 구성도
    └── configs/         # 이 랩에서 사용한 장비별 설정
```

## 랩 README 템플릿

```markdown
# 랩 이름

## 목표
- 이 랩에서 확인하려는 것

## 토폴로지
![topology](topology.png)

## 주소 체계
| 장비 | 인터페이스 | IP 주소 |
|---|---|---|

## 주요 설정
(핵심 설정만 발췌)

## 검증
(show 명령어 출력, ping/traceroute 결과)

## 배운 점 / 트러블슈팅
```
