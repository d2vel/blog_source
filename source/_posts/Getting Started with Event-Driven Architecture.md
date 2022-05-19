---
title: Getting Started with Event-Driven Architecture
date: 2022-05-02T08:00:22+09:00
tags:
  - eventdriven
  - AWS
categories:
  - Architecure
---

{% asset_img 2022-05-02T080133.png MSA %}

참조 : https://aws.amazon.com/ko/blogs/compute/getting-started-with-event-driven-architecture/

간단하게 이벤트 드리븐 아키텍쳐에 대해서 알아봅시다.

<!-- more -->

현대적인 어플리케이션에서 Event Driven 아키텍쳐는 클라우드에서 어플리케이션을 좀 더 쉽게 만들수 있게 해주기 때문에 더욱 중요해지고 있다. Event Driven 아키텍쳐는 서비스들을 decouple 할 수 있게 되므로, 개발 속도를 향상 시키고, 어플리케이션의 디버깅을 좀 더 쉽게 합니다. 또한 이 아키텍쳐는 기능이 여러 팀안에서 확장될 때 병목현상(bottlenect)를 제거하고 이것은 팀이 좀 더 독립적으로 개발을 진행할 수 있도록 합니다.

프로그램이 어떻게 동작하는가 생각할 수 있는 한 가지 방법은 어플리케이션의 다른곳으로 부터 발생하는 이벤트에 반응하는 시스템으로 보는 것입니다. 이같은 접근은, 이벤트 전달자로써 그것을 둘러쌓고 있는 것들과 시스템의 상호작용에 초점을 맞춥니다. 어플리케이션은 이벤트를 받고 이벤트를 만들어낸다. 애플리케이션으로의 입력과 애플리케이션으로 부터의 출력은 이벤트로써 동작합니다. 이것이 Event Driven 아키텍쳐의 핵심입니다.

## API-Driven architecture vs Event-Driven architecture

| Commands/APIs                          | Events                               |
| -------------------------------------- | ------------------------------------ |
| Synchronous                            | Asynchronous                         |
| Has an Intent</br>Driected to a target | It's a fact</br>Happened in the past |
| "CreateAccount"</br>"AddProduct"       | "AccountCreated"</br>"RoductAdded"   |

응답과 요청을 기반으로 하는 API Driven 아키텍쳐는 여러 컴포넌트들이 함께 동작할 수 있게 해주는 애플리케이션을 만드는 일반적인 방법이다. 예를 들어, 주문 API로 부터 주문의 쿼리를 요청한다면, 주문 API는 주문리스트를 반환할 것이다. 이것은 동기적인 아키텍텨의 예제이다. 주문을 요청하는 시스템은 응답을 기다립니다. 응답이 오기 전에는 다른 곳으로 이동할 수가 없습니다. 이같은 접근에서는 명령어를 직접적으로 Target에 보냅니다.(예를 들어, "place this order" 또는 "add this record to the database").

{% asset_img 2022-05-02T083202.png sync and async %}

이러한 동기화 모델 안에서, 클라이언트는 Service A로 요청을 한다. Service A는 Service B를 호출한다. 그러나 Service A는 계속 진행하거나 이벤트의 응답을 보내기 전에, Service B의 응답을 기다린다.

비동기인 Event Driven 아키텍쳐에서는 응답 경로가 없습니다. 서비스는 이벤트를 발생시킨 후 즉시 다음으로 이동합니다. 여기서의 Trade-off는 이벤트를 수신했다는 것외에 Service A로 정보를 전달하기 위한 Service B에 대한 직접적인 채널이 없다는 것이다. 그러나 많은 경우에 요청 채널과 응답 채널 간에 명시적인 결합(coupling)이 필요하지 않습니다.

이벤트는 이미 발생한 어떤 것입니다. 예를 들어, 새로운 계정이 생성되었거나(is created), Amazon S3 버킷으로 아이템이 전송(is dropped)이 된 경우 입니다. 예를 들어, 만약 주문이 되었을 때 이벤트가 발생한다면, 주문이 취소되는 이벤트도 존재할 수 있습니다. 이벤트는 메시징 시스템 또는 데이터베이스와 같은 다양한 것으로부터 가져올 수 있습니다.

이벤트는 응용 프로그램에서 발생한 사항에 대한 정보를 알려주는 JSON 개체입니다. 이벤트 중심 아키텍처에서 이벤트는 사실을 나타냅니다. 애플리케이션의 각 구성 요소는 변경 사항이 있을 때마다 이벤트를 발생시킵니다. 다른 구성 요소들은 듣고 이를 사용하여 무엇을 할 것인지, 그리고 어떻게 반응할 것인지를 결정합니다.

{% asset_img 2022-05-02T084558.png event Json example %}

위의 이벤트는, Amazon S3 버킷에 이미지가 담겨졌을 때 S3가 발생하는 이벤트 입니다. Event Source는 sam-app-sourcebucket라는 이름을 가진 S3 버킷입니다. 이 버킷에 담겨진 Object는 "brad.jpeg" 입니다.

요청 기반의 어플리케이션은 요청을 완료하기 위해서 항상 다운스트림 함수에 직접적으로 명령을 전달하고 이것은 강하게 결합이 됩니다. 이것은 어플리케이션에서 오류가 언제 방생하는 것이지 결정하는 것을 어렵게 합니다. 이벤트 기반의 어플리케이션은 다른 서비스나 시스템에서 활용되어지는 이벤트를 발생합니다. 그러나 이벤트 생성하는 쪽에서는 이벤트를 어떠한 소비자(consumer)를 알지 못합니다. 이것은 전형적으로 느슨합 결합의 형태를 가지게 됩니다.

이벤트를 볼 수 있는 권한을 가진 어떠한 서비스라도 이벤트를 관찰할 수 있습니다. 커피숍의 예제를 생각해본다면 거기에는 커피를 내리고 있는 바리스타가 있고, 그리고 pastries를 만드는 파티쉐가 있습니다. 고객이 커피숍으로 들어오고 그리고 커피를 주문하면 파리스타는 커비를 내리기 시작하고 파티쉐는 아무 행동도 하지 않습니다.

그러나 만약 고객이 커피숍에 들어와 초콜릿 크로아상을 주문한다면 파티쉐는 초콜릿 크로아상을 만들기 시작합니다. 그리고 바리스타는 아무 행동을 하지 않을 것입니다. 파티쉐는 오직 파티쉐와 관련된 주문만을 관심하고 있고, 바리스타는 오직 커피와 관련된 주문에만 관심을 두고 있습니다.

아마존과 같은 E-commerce 에플리케이션 관점에서 본다면, 다른 이벤트에 응답을 주엉야 할 각각의 부서들이 있습니다. 아마존에서 Amazon Fresh의 Whole Food를 주문할 수 있다. Amazon Fresh에서 주문은 한다면 이 이벤트를 구독하고 있는 부서에는 그 이벤트를 처리하고 주문을 수행할 것이다.

{% asset_img 2022-05-04T081857.png event Json example %}

이벤트 기반의 아키텍쳐와 커맨드 기반의 아키텍쳐는 각각 다른 방식으로 상태를 저장합니다. 전형적인 커맨드 기반의 아키텍쳐에서는, 컴포넌트들은 각 데이터의 최종상태를 업데이트를 수행합니다. 이벤트 기반의 아키텍쳐에서는 업데이트를 수행할 때 새로운 이벤트가 발생하도록 하면 됩니다. 설명하면 커멘드 기반의 아키텍쳐에서는 업데이트의 최종 업데이트 상태를 가지고 있지만, 이벤트 기반의 아키텍쳐에서는 발생한 모든 이벤트 전체를 가지고 있습니다.

## 이벤트 기반 아키텍쳐의 장점

### 이벤트의 소스와 Targer를 분리

많은 어플리케이션들은 각 컴포넌트가 강하게 결합되어 있고 서로가 강하게 의존하는 Monolith로 구성이 됩니다. 이것은 버그가 있고 애플리케이션의 실패하는 곳이 어느 부분인지 정확히 찾아내는 것에서 문제가 되는 것으로 판명되었습니다. 분리된 아키텍쳐는 느슨하게 결합된 서비스나 컴포넌트로 구성이 됩니다. 이벤트 주도적인 분리된 아키텍쳐에서는 이벤트는 누가 응답하는지 알지 못한채로 이벤트를 브로드캐스트 합니다. 이벤트를 수신하는 컴포넌트나 서비스가 이벤트를 처리할 준비가 될 때마다 이벤트를 대기열에 넣고,전달할 수 있기 때문에 이것은 좀 더 확장가능하고 변경에 열려있는 시스템을 개발할 수 있도록 합니다.

Decoupled 된 아키텍쳐는 팀이 독집적으로 행동할 수 있게 해주고 이는 개발 속도를 증가시킨다. 예를 들어 API를 기반으로 한 통합에서는 만약 내 팀이 다른 팀의 마이크로서비스에서 일어나는 변경에 대해서 알기를 원한다면 나는 나의 서비스를 위해서 그 팀이 만드는 API에 대해서 물어봐야 한다. 이것은 나는 다른 팀과 인증을 적절하게 처리해야 하며, 다른 팀과 API 호출을 위한 구조들에 대한 협의가 필요하다는 것을 의미합니다. 이것은 팀 사이를 왔다갔다하는 것을 야기시키며, 개발속도를 낮춥니다. 이벤트 주도적인 아키텍쳐에서는 마이크로서비스나 이벤트의 라우팅을 처리하고, 인증을 처리해주는 이벤트 라우터(예를 들어, Amazon EventBridge) 로부터 이벤트를 구독합니다.

Decoupled 어플리케이션은 새로운 기능을 보다 빨리 개발할 수 있게 합니다. 새로운 기능을 추가하는 것과 기존에 있는 기능을 확징하는 것은 이벤트 주도적인 아키텍쳐에서는 보다 간단한 일입니다. 새로운 기능을 실행하고 구독하는 데 필요한 이벤트만 선택하면 되기 때문입니다. 새로운 기능을 추가하기 위해 기존 서비스를 수정할 필요가 없습니다.
 
### Write less code

When you build applications using event-driven architecture, often you write less code because you only need to consider new events, as well as which service is subscribed to those events. For example, if you are building new features for your application, all you have to do is consider the existing events and then add senders and receivers as necessary. In this way, you speed up development time because each functional unit is smaller and there is often less code.

### Better extensibility

### Enhancing team collaboration

## 결론