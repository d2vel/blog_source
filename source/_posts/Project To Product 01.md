---
title: 비즈니스 결과로 연결하기
date: 2022-05-03T11:51:36+09:00
tags:
  - Culture
  - ProjectToProduct
categories:
  - Project To Product
---

{% asset_img 2022-05-03T115153.png 플로우 지표과 비즈니스 결과 연결하기 %}

이번에는 서버리스를 도입하고 운영하기 위해서 어떤 점들을 고려해봐야 하는지에 대해서 작성해 보았습니다.

<!-- more -->

위의 그림은 플로우 지표와 비즈니스 지표를 어떻게 연결할 수 있는지를 보여주고 있다. 플로우 지표들은 살펴보았고(플로우 속도, 플로우 효율, 플로우 타입, 플로우 부하), 비즈니스 지표들은 이제 살펴보도록 하겠다. 아래의 표는 간단하게 이를 요약하고 있다.

| 비즈니스 결과 | 측정 방법                                          | 예시                                                   |
| ------------- | -------------------------------------------------- | ------------------------------------------------------ |
| 가치          | 가치 흐름이 생산하는 비즈니스 이득                 | 매출, 월간 고청 매출, 연간 계약 가치, 월간 활성 사용자 |
| 비용          | 비즈니스가 부담하는 가치 흐름 비용                 | 가치 흐름을 위한 모든 인건비, 운영비, 인프라 비용, 가치 흐름에 할당된 전일제 환산(FTE, Full Time Equivalent)                                                       |
| 품질          | 가치 흐름이 생산하는 제품이 고객에게 감지되는 품질 | (고객에게) 드러난 결함, 쌓인 티켓 수, 계약 연장률, 확장률, 순수 고객 추천 지수(NPS)                                                       |
| 행복도        | 가치 흐름 구성원의 몰입도                          | 직원 추천 지수(eNPS), 직원 몰입                                                       |

## Measuring Value : 가치 측정하기

  - 측정 지표는 조직에서 사용하는 재무 지표에서 직접 가져와야 한다. 
  - 가치 흐름에 의해 생성된 수익 선행 지표, 즉 세이즈 파이프 라인 성장률, 고객 만족도(예를 들어 NPS)등을 가치 버킷에 넣을 수 있다. 

## Value Stream Cost : 가치 흐름 비용
 
  -  가치 흐름 비용은 특정 제품의 전달과 관련된 모든 비용을 포함한다. 해당 가치 흐름의 전달과 관련된 모든 것을 포함해 TCO(Total Cost of Ownership)을 고려하는 것과 비슷하며 제조업의 '가치 흐름별 비용 계산(Costing by value stream)' 기법과도 유사하다. 
  - 가치 흐름 비용은 직접적인 비용뿐만 아니라 가치 흐름이 사용하는 공유 서비스 비용의 비율도 계산해야 한다. 여기에는 인건비(정직원 또는 계약직), 라이선스 비용, 인프라 비용(내부 또는 외부 호스팅) 등이 포함될 수 있다. 

## Value Stream Quality : 가치 흐름 품질

  - 가치 흐름은 고객에게 초점을 두고 있으므로 품질 버킷에는 고객에서 드러나는 종류의 지표를 사용해야 한다. 드러난 결함 뿐만 아니라 발생한 사건의 수, 티켓 개수, 기타 고객 만족 지표도 품질 버킷에 포함될 수 있다. 결함 수명(Defect Aging: 결함 인지부터 해소까지의 기간) 및 변경 성공률과 같이 고객에게 인지되지 않는 품질 지표는 품질 문제의 중요한 선행 지표이기는 하지만 플로우 프레임워크는 고개과 비즈니스에 가시적인 지표에 집중하므로 중요는 한 단계 아래이다.

## Value Stream Happiness

  - 가치 흐름의 생산성은 화면 디자인, 자동화 테스트 작성 등 가치 창출 활동에 달려 있다. 앞으로 인공 지능이 테스트 코드를 자동으로 작성하는 것과 같이 자동화되는 분야가 늘어나더라도, 생산성은 언제나 팀에서 수행하는 창조적 작업에 달려 있음을 기술 기업들은 오래전부터 잘 알고 있었다. 
  - 성과가 좋은 조직은 이미 eNPS(직원 추천 지수)와 같은 지표를 통해 직원 참여도를 측정한다. Accelerate는 IT 조직에서 좋은 성과를 내느 조직의 직원이 그들의 조직을 일하기 좋은 곳으로 추천하는 경향이 2.2배 더 높음을 발표했다.

## Value Stream Dashboards

{% asset_img ValueStreamDashboards.png 플로우 지표과 비즈니스 결과 연결하기 %}
