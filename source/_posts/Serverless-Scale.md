---
title: Serverless Scale
date: 2022-04-30 12:40:27
tags:
  - Serverless
  - Lambda
  - AWS
categories:
  - AWS
---

{% asset_img hurdle.jpg This is an example image %}

이번에는 서버리스에 대해서 한번 살펴보고자 한다. 대학교 때부터 이런 이야기를 IT에서 자주 들어왔던 것 같다. 뭔가를 동작하게 하는 것은 쉽지만 잘 동작하게 하는 것은 매우 어려운 일이다. 이것은 특히 회사에서 공감을 많이 하게 되는 말이었다. 회사에서 항상 뭔가를 하다보면 POC/BMT 레벨에서는 문제가 없었지만 실제 운영에서는 문제가 생기는 경우를 많이 보았다. 대부분은 생각하지 못했던 예외 케이스와 미리 생각하지 못했던 부하에서 문제많이 생겼다.

두 번째 하고 싶은 이야기는 언제나 새로운 기술의 도입에는 허들이 있다는 것이다. Spring Boot, JPA, DDD등 모두가 그랬다. JPA를 처음 사용하려고 교육에 갔을 때 강사가 했던 말은 "JPA는 성능이 좋지 않기 때문에 실제 SI환경에서는 사용하지 않습니다." 였습니다. 이 말을 듣고 실제 이것의 성능이 그렇게 좋지 않다면 외국에서 이것을 많이 사용할까 였습니다. 하지만 JPA를 사용하면서도 성능을 유지하려면 CQRS 및 Query DSL등에 대한 지식도 같이 있어야 성능의 문제를 해결 수 있었습니다. 말씀 드렸다시피 어떤 시도가 성공의 괘도에 오르려면 허들을 넘는 것은 필수 입니다. **허들을 넘지 못하면 새로움은 없습니다.** 또한 대부분의 허들을 넘지 못하는 사람들이 하는 말은 그 기술은 좋지 않다입니다. ㅠㅠ

## 서버리스

서버리스는 말 그래도 서버가 없다는 말이다. 하지만 실제로 서버가 없지는 않겠죠? 이 말은 서버를 별도로 관리할 필요가 없다는 것이다. 서버를 만들 필요도 서버의 최신 버젼을 유지한다거나 서버를 패치해야 할 필요도 없다. 일반적으로 아주 간단하게 말하면 서버의 Scale도 관리할 필요는 없습니다. **하지만 고려해야 할 필요는 있습니다.**.

많은 CSP등 혹은 K8S도 서버리스에 대한 서비스를 제공하고 있습니다.

{% asset_img serverless_services.png This is an example image %}

AWS 서비스들을 보면 그 중심에 Lambda가 있습니다. 당연히 서버리스를 사용해서 서비스를 만든다면 일단 이들에 대해서 정확하게 하는 것이 필요합니다.

### 서버리스 도입 및 운영의 장점

서버리스는 일반적으로 다른 서비스에 비해 일반적인 상황에서 운영에 대해 고려할 것이 적습니다. 실제 시스템의 패치 및 부하에 따른 고려, 또한 시스템의 구성 등에 시간이 적게 들어갑니다. 대표적인 서버리스 compute 컴포넌트인 lambda의 경우를 예를 들어 설명해 보면 Serverless Framework이나 SAM을 활용하면 매우 짧은 시간 안에 시스템의 구성이 가능합니다.

또한 Dyanmodb / Amplify / Cognito 등과 같이 사용을 할 때는, Compute 뿐만 아니라 Database / Auth들의 구현도 매우 짧은 시간 안에 구현할 수 있습니다.

{% asset_img 2022-04-30T173334.png This is an example image %}

### 서버리스를 도입할 때 고려해야 할 것들

하지만 일반적인 상황에서 서버리스가 매우 빠른 다른 이야기 입니다. 갑작스럽게 부하게 엄청 많이 증가하는 상황이라든지 혹은 엄청 연산이나 처리가 길어지는 경우에는 컨드롤하기 어려운 단점이 존재합니다. 또한 lambda의 경우는 요청 수행시간에 따라 Concurrency가 매우 많은 영향을 받습니다. 또한 기본적으로 Account Quota가 아래와 같이 설정되어 있습니다.

| Resource                                                                                                                                                                                                                                                  |                  Default quota | Can be increased up to |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------------------: | :--------------------: |
| Concurrent executions                                                                                                                                                                                                                                     |                          1,000 |   Tens of thousands    |
| Storage for uploaded functions (.zip file archives) and layers. Each function version and layer version consumes storage. <br /><br /> For best practices on managing your code storage, see Monitoring Lambda code storage in the Lambda Operator Guide. |                          75 GB |       Terabytes        |
| Storage for functions defined as container images. These images are stored in Amazon ECR.                                                                                                                                                                 | See Amazon ECR service quotas. |                        |
| Elastic network interfaces per virtual private cloud (VPC)<br />Note<br />This quota is shared with other services, such as Amazon Elastic File System (Amazon EFS). See Amazon VPC quotas.                                                               |                            250 |        Hundreds        |

위와 같이 스케일이 제한되어 있기 때문에 특별히 Burst 상황이 생길 여지가 있는 곳에서는 고려해야 할 것 들이 생기게 됩니다. 아래는 특별히 lambda의 부하 및 구성관점에서 어떠한 것들을 고려해야 하는지 알아보도록 하겠다.

#### 큰 API는 Lambda를 Account로 분리해야 한다.

{% asset_img 2022-04-30T175208.png This is an example image %}

https://aws.amazon.com/ko/blogs/compute/managing-cross-account-serverless-microservices/ 참조

위와 같이 Account로 분리해서 부하를 받아줄 수 있도록 분산해야 합니다. 하지만 이를 위해서 CI/CD Pipeline 구성의 복잡도를 포함해야 합니다. 이에 대해서는 아래와 같이 좀더 자세히 기술하도록 하겠습니다.

#### 짧은 Lambda 수행시간

1,000 Concurrency는 많은 수이지만 경우에 따라서는 매우 작은 숫자일 수도 있습니다.이 한계 안에서 Lambda를 활용하기 위해서는 Lambda의 수행시간을 가능하면 짧게 가져가야 합니다. 이를 위해서 EDA(Event Driven Architecure)와 함께 Nosql(Dynamodb)를 활용하여 처리시간을 짧게 가져가야 합니다.

{% asset_img 2022-04-30T180532.png This is an example image %}

참고: https://aws.amazon.com/ko/blogs/compute/operating-lambda-understanding-event-driven-architecture-part-1/

#### Cold Start

{% codeblock lang:javascript %}
const AWS = require('aws-sdk');
// http or https
const http = require('http');
const agent = new http.Agent({
    keepAlive: true,
    // Infinity is read as 50 sockets
    maxSockets: Infinity
    });

AWS.config.update({
    httpOptions: {
    agent
    }
});
{% endcodeblock %}
