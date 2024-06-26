
카프카(Kafka)`는 Apach 재단의 pub-sub 모델의 메시징 시스템 입니다. ActiveMQ, Artmeis, RabbitMQ와 유사한 메시지 브로커입니다. 그러나 카프카(Kafka)는 특유의 아키텍처를 가지고 있습니다.

카프카(Kafka)는 높은 확장성을 제공하는 클러스터로 실행되도록 설계되어 있습니다. 또한 `fail-over`, `replication`와 같은 여러가지 특징들을 가지고 있습니다. 

## 1. Pub - Sub 모델

카프카(Kafka)는 `pub-sub(발행/구독)` 모델을 사용합니다. pub-sub는 메시지를 특정 수신자에게 직접적으로 보내주는 시스템이 아닙니다. publisher는 메시지를 topic을 통해서 카테고리화 합니다.


## 2. 토픽(Topic)

카프카(Kafka)는 데이터를 최종적으로 토픽(Topic)이라는 곳에 저장을 하며 데이터를 구분하기 위한 분류값 혹은 구분된 저장소라 이해하면 됩니다. 

카프카는 데이터를 주고 받을 때 지정된 토픽으로 주고 받게 되며, 설계를 어떻게 할 것인지가 관건입니다.


## 3. 파티션(Partition)

메시지가 토픽(Topic)으로 분류되고, 토픽(Topic)은 여러개의 파티션으로 나눠질 수 있습니다. 파티션내의 한 칸은 로그라고 불립니다. 데이터는 한 칸의 로그에 순차적으로 append 됩니다. 메시지의 상대적인 위치를 나타내는게 offset인데, 배열에서 index 개념이라고 생각하면 쉽게 이해됩니다.

토픽(Topic)에 여러개의 파티션을 두는 경우에는 몇 천건의 메시지가 동시에 카프카(Kafka)에 쓰여진다고 생각해보면 하나의 파티션만 둘 경우 순차적으로 append가 발생하게 되는데, 그렇게 되면 카프카 입장에서는 처리하기가 무겁습니다. 그렇기 때문에 여러개의 파티션을 두어서 분산저장을 하는 것입니다. 

그만큼 시간이 절약된다는게 핀트입니다. 하지만 이것도 트레이드오프(trade-off)로 한 번 늘린 파티션은 절대로 줄일 수 없기 때문에 운영 중에, 파티션을 늘려야하는건 항상 고려해야할 사항입니다.

> 파티션에 메시지가 `Round-robin` 방식으로 쓰여집니다. 즉 순차적으로 메시지가 쓰여지지 않습니다. 만약 해당 토픽을 소비하는 소비자가 만약 메시지의 순서가 엄청 중요한 모델이라면 순차적으로 소비됨을 보장해주지 않기 때문에 상당히 리스크가 커집니다.


## 4. Producer

Producer는 메시지를 생산하는 주체입니다. 메시지를 만들고 Topic에 메시지를 씁니다. Producer는 Consumer의 존재를 알지 못합니다. 단지 카프카(Kafka)에 메시지를 쓰는 주체라고 생각하면 됩니다.


## 5. Consumer

Consumer는 소비자로써 메시지를 소비하는 주체입니다. 역시 Producer의 존재를 모릅니다. 해당 토픽(Topic)를 구독함으로써, 자기가 스스로 조절해가면서 소비할 수 있는 것입니다. 

소비를 했다는 표시는 해당 토픽(Topic)내의 각 파티션에 존재하는 offset의 위치를 통해서 이전에 소비했던 offset위치를 기억하고 관리하고 이를 통해서, 혹시나 Consumer가 죽었다가 다시 살아나도, 이전에 마지막으로 읽었던 위치에서부터 다시 읽어들일 수 있습니다. 그렇기 대문에 `fail-over`에 대한 신뢰가 존재합니다.

## 6. Consumer Group

Producer에서 생산한 메시지는 여러개의 파티션에 저장을 하는데, 그렇다면 소비하는 쪽에서도 여러 소비자가 메시지를 읽어가는 것이 훨씬 효율적일 것입니다. 하나의 목표를 위해 소비를 하는 그룹, 즉 하나의 토픽을 읽어가기 위한 Consumer들을 Consumer Group라고 합니다.

Consumer Group가 필요한 또 한가지 이유는 데이터를 병렬로 읽게 되어 빠른처리가 가능하다는 부분도 있겠지만, 특정 컨슈머에 문제가 생겼을 경우 다른 그룹내 컨슈머가 대신 읽을 수 있게 리벨런싱이 되어 장애 상황에서도 문제없이 대처할 수 있게 됩니다.

> Consumer Group은 한가지 룰이 있습니다. Topic과 Consumer Group는 `1:N`의 관계를 가집니다. 즉, 자신이 읽고 있는 파티션에는 같은 그룹내 다른 컨슈머가 읽을 수 없습니다. 
보통 파티션 개수와 컨슈머 그룹내 컨슈머 수를 맞추는것을 추천하기도 합니다. 


## 7. 주키퍼란?

주키버는 분산 어플리케이션을 위한 코디네이션 시스템 입니다. 

분산되어있는 각 애플리케이션의 정보를 중앙에 집중하고 구성 관리, 그룹 관리 네이밍, 동기화 등을 제공합니다.

상태 정보를 지노드(znode)라 불리는 곳에 key-value 형태로 저장합니다. 이 지노드에 저장된 key-value를 이용하여 분산 어플리케이션은 서로 데이터를 주고 받습니다.

지노드는 일반 컴퓨터의 파일이나 폴더 개념으로 생각하면 이해하기 쉽습니다.


## 8. 카프카 클러스터 구조

![image](https://user-images.githubusercontent.com/22395934/128202447-7b35e28a-27d3-4461-a111-b0dd4ee5dba7.png)


## 9. 세그먼트

프로듀서에 의해 브로커로 전송된 메시지는 토픽의 파티션에 저장됩니다. 각 메시지들은 세그먼트라는 로그 파일의 형태로 브로커의 로컬 디스크에 저장됩니다. 

각 파티션마다 N개의 세그먼트 로그 파일들이 존재합니다. 
브로커 서버에 접속하면 `토픽-파티션번호` 형식의 디렉토리가 생성되어 있고 해당 디렉토리에서 로그 파일을 열어서 프로듀서가 보낸 메시지 확인이 가능합니다.


## 10. 리플리케이션

고가용성 분산 스트리밍 플랫폼인 카프카는 무수히 많은 데이터 파이프라인의 정중앙에 위치하는 메인 허브 역할을 합니다. 이런 역할을 하는 카프카 클러스터가 만약 하드웨어의 문제나 점검 등으로 인해 정상적으로 동작하지 못한다거나, 카프카와 연결된 전체 데이터 파이프라인에 영향을 미친다면 이는 매우 심각한 문제가 됩니다.

이때 안전성을 확보하기 위해 카프카 내부에서는 `리플레이션`이라는 동작을 하게 됩니다.
카프카는 브로커의 장애에도 불구하고 연속적으로 안정적인 서비스 제공함으로써 데이터 유실을 방지하며 유연성을 제공합니다. 기본적으로 카프카의 토픽 생성 시 필수 값으로 `replication facotr`라는 옵션을 설정해야 합니다. 

보통 경험많은 카프카 운영자들은 replication factor를 3으로 설정하는게 리소스를 낭비하지 않고 안정적으로 카프카 운영이 가능하다고 합니다.

명령어 예시는 아래와 같습니다. 카프카의 기본 도구인 `kafka-topics.sh` 명령어를 이용해 토픽을 생성합니다.

```sh
/user/local/kafka/bin/kafka-topics.sh --bootstrap-server {도메인:포트} --create --topic {토픽명} --partitions 1 --replication-factor 3
```

카프카 리플리케이션 팩터라는 옵션을 이용해 관리자가 지정한 수만큼의 리플리케이션을 가질 수 있기 때문에 N개의 리플리케이션이 있는 경우 N-1까지의 브로커 장애가 발생해도 메시지 손실 없이 안정적으로 메시지를 주고 받을 수 있습니다.

### 리더와 팔로워

리플리케이션은 리더와 팔로워로 구분됩니다. 오로지 리더를 통해서만 읽기와 쓰기가 가능합니다. 다시 말해, 프로듀서는 모든 리플리케이션에 메시지를 보내는 것이 아니라 리더에게만 메시지를 전송합니다. 또한 컨슈머는 오직 리더로부터 메시지를 가져옵니다.

![Untitled Diagram drawio (1)](https://user-images.githubusercontent.com/22395934/144257447-1560aa90-ce12-46a4-812f-0bd65a07ddb7.png)

위 그림에서는 프로듀서와 컨슈머, 리더의 관계를 그림으로 표현했습니다. peter-test01 토픽의 파티션 수는 1이고, 리플리케이션 팩터수는 3입니다. peter-test01-0은 peter-test01 토픽의 0번 파티션이라는 뜻입니다.

프로듀서는 peter-test01 토픽으로 메시지를 전송하는데, 파티션의 리더만 읽고 쓰기가 가능하므로 0번 파티션의 리더로 메시지를 보냅니다. 컨슈머 동작에도 역시 0번 파티션의 리더로부터 메시지를 가져옵니다. 팔로워는 대기하는게 아니고 리더에 문제가 발생하거나 이슈가 있을 경우 대비해 언제든지 새로운 리더가 될 준비를 해야합니다. 따라서 컨슈머가 토픽의 메시지를 꺼내 가는 것과 비슷한 동작으로 지속적으로 파티션의 리더가 새로운 메시지를 받았는지 확인하고, 새로운 메시지가 있다면 해당 메시지를 리더로부터 복제합니다.

## 카프카의 장점

다른 타 메시징 시스템들은 리더와 팔로워가 메시지를 잘 받았는지 ACK 통신을 하지만 카프카는 ACK 통신 단계를 제거하였습니다.

메시지가 별로 없으면 한 두번의 ACK 통신을 서로 주고받는것은 성능상 별 다른 문제가 없지만, 카프카처럼 대량의 메시지를 처리하는 애플리케이션은 이러한 작은 차이에도 크게 부각 됩니다. 

만약 메시지 하나를 리플리케이션하는데 총 2회의 ACK 통신을 주고받는다고 가정하면 1000개의 메시지는 2000회의 ACK 통신이 필요하며, 1만개의 메시지는 2만회의 ACK 통신이 필요합니다. 이렇게 ACK 통신을 제외한 카프카의 리더는 메시지를 주고받는 기능에 더욱 집중할 수 있습니다.

또 다른 장점은 리플리케이션 동작에서 ACK 통신을 제외했음에도 불고하고 팔로워와 리더 간의 리플리케이션 동작이 매우 빠르면서도 신뢰할 수 있다는 점입니다. 카프카에서 리더와 팔로워들의 리플리케이션 동작 방식은 리더가 푸쉬하는 방식이 아니라 팔로워들이 풀하는 방식으로 동작하는데, 풀 방식을 채택한 이유도 리플리케이션 동작에서 리더의 부하를 줄여줄기 위해서 입니다.

## 리더에포크

리더에포크는 카프카의 파티션들이 복구 동작을 할 때 메시지의 일관성을 유지하기 위한 용도로 이용됩니다.

리더에포크는 컨트롤러에 의해 관리되는 32비트의 숫자로 표현됩니다. 해당 리더에포크 정보는 리플리케이션 프로토콜에 의해 전파되고 새로운 리더가 변경된 후 변경된 리더에 대한 정보는 팔로워에게 전달됩니다.

만약 리더에포크가 없다면 팔로워는 자신의 하이워타마크 보다 앞에 있는 메시지를 무조건 삭제하게 됩니다. 그리고 팔로워가 리더로 승격이 되면 해당 오프셋의 메시지는 하이워터마크보다 앞에 있다는 이유만으로 메시지 손실이 발생하게 되어 안전성이 떨어지게 됩니다.

이러한 이유 때문에 팔로워 장애 시 다시 복구 작업을 위해서 리더에게 리더에포크를 요청하게 되는데 이 경우에는 하이워터마크 앞에 있는 메시지를 삭제하지 않고 리더로부터 리더에포크를 요청하여 응답으로 현재 오프셋의 메시지 정보를 응답으로 받고 하이워터마크를 해당 오프셋의 메시지까지 자신의 하이워터마크를 상향 조정합니다.

## 파티셔너

카프카의 토픽은 성능 향상을 위한 병렬 처리가 가능하도록 하기 위해 파티션으로 나뉘고, 최소 하나 또는 둘 이상의 파티션으로 구성됩니다. 

그리고 프로듀서가 카프카로 전송한 메시지는 해당 토픽 내 각 파티션의 로그 세그먼트에 저장됩니다. 따라서 프로듀서는 토픽으로 메시지를 보낼 때 해당 토픽의 어느 파티션으로 메시지를 보내야 할지를 결정해야 하는데, 이 때 사용하는 것이 바로 파티셔너입니다. 프로듀서가 파티션을 결정하는 알고리즘은 기본적으로 메시지(레코드)의 키를 해시처리해 파티션을 구하는 방식을 사용합니다. 

따라서 메시지의 키값이 동일하면 해당 메시지들은 모두 같은 파티션으로 전송됩니다.

만약 예상치 못한 많은 양의 메시지들이 카프카로 인입되는 경우, 카프카는 클라이언트의 처리량을 높이기 위해 토픽의 파티션을 늘릴 수 있는 기능을 제공합니다. 이때 파티션 수가 변경됨과 동시에, 메시지의 키와 매핑된 해시 테이블도 변경 됩니다. 따라서 프로듀서가 동일한 메시지의 키를 이용해 메시지를 전송하더라도 파티션의 수를 늘린 후에는 다른 파티션으로 전송될 수도 있습니다.

## Kafka 커밋 정보 확인하기

브로커는 재시작될 때 커밋된 메시지를 유지하기 위해 로컬 디스크의 replication-offset-checkpoint라는 파일에 마지막 커밋 오프셋 위치를 저장합니다.

해당 파일은 브로커 설정 파일에서 설정한 로그 디렉토리의 경로에 있으며, 브로커 설정 파일의 로그 디렉토리는 `/data/kafka-logs`로 설정되어 있으므로, 해당 디렉토리 하위에 위치합니다. 

```kafak
peter-test01 0 1
```

peter-test01은 토픽을 의미하며, 0은 파티션 번호, 1은 커밋된 오프셋 번호를 뜻합니다.


## 컨슈머 오프셋 관리

컨슈머 동작 중 가장 핵심은 바로 오프셋 관리입니다. 컨슈머는 카프카에 저장된 메시지를 꺼내오는 역할을 하기 때문에 컨슈머가 메시지를 어디까지 가져왔는지를 표시하는 것은 매우 중요합니다.

만약 코드 배포로 인해 컨슈머가 일시적으로 동작을 멈추고 재시작하는 경우나, 컨슈머가 구동중인 서버에서 문제가 발생해 새로운 컨슈머가 기존 컨슈머의 역할을 대신하는 경우에는 기존 컨슈머의 마지막 메시지 위치부터 새로운 컨슈머가 메시지를 가져올 수 있어야만 장애로부터 빠르게 복구될 수 있습니다.

카프카에서는 메시지의 위치를 나타내는 위치를 `오프셋(offset)`이라고 부릅니다. 이 오프셋은 숫자 형태로 나타냅니다. 오프셋은 0번부터 시작되는데 Java 배열에서 인덱스를 떠오르시면 이해가 쉽습니다.

컨슈머 그룹은 자신의 오프셋 정보를 카프카에서 가장 안전한 저장소인 토픽에 저장합니다. 즉, `_consumer_offsets` 토픽에 각 컨슈머 그룹별로 오프셋 위치 정보가 기록됩니다. 

컨슈머그룹은 _consumer_offsets에 컨슈머 그룹, 토픽, 파티션 등의 내용을 통합해 기록합니다. 이렇게 기록된 정보를 이용해 컨슈머 그룹은 자신의 그룹이 속해 있는 컨슈머의 변경이 발생하는 경우(장애나 이탈 등) 해당 컨슈머가 어느 위치까지 읽었는지를 추적할 수 있습니다.

여기서 저장되는 오프셋 값은 컨슈머가 마지막까지 읽은 위치가 아니라 컨슈머가 다음으로 읽어야할 위치를 말합니다.

## 컨슈머 그룹 등록 

컨슈머 그룹 관리를 위해 별도의 코디네이터가 브로커 서버에 존재하는데 이를 카프카에서는 `그룹 코디네이터`라고 부릅니다.

그룹 코디네이터의 목적은 컨슈머 그룹이 구독한 토픽의 파티션들과 그룹의 멤버들을 트래킹하는 것입니다. 따라서 파티션 또는 그룹의 멤버에 변화가 생기면, 작업을 균등하게 재분재하기 위해 컨슈머 리벨런싱 동작이 발생합니다.

그룹 코디네이터는 각 컨슈머 그룹별로 존재하며, 이러한 그룹 코디네이터는 카프카 클러스터 내의 브로커 중 하나에 위치합니다.

먼저 그룹 코디네이터와 컨슈머 그룹의 관계를 살펴보겠습니다.

컨슈머 그룹이 브로커에 최초 연결 요청을 보내면 브로커 중 하나에 그룹 코디네이터가 생성되고, 이 그룹 코디네이터는 컨슈머 그룹의 컨슈머 변경과 구독하는 토픽의 파티션 변경 등에 대한 감지를 시작합니다. 그리고 토픽의 파티션과 그룹의 맴버 변경이 일어나면 변경된 내용을 컨슈머들에게 알려주기도 합니다.

1. 컨슈머는 컨슈머 설정값 중에서 `bootstrap.brokers` 리스트에 있는 브로커에게 컨슈머 클라이언트와 초기 커넥션을 연결하기 위한 요청을 보냅니다. 

2. 해당 요청을 받은 브로커는 그룹 코디네이터를 생성하고 컨슈머에게 응답을 보냅니다. 컨슈머 그룹의 첫 번째 컨슈머가 등록될 때까지 아무 작업도 일어나지 않습니다.

3. 그룹 코디네이터는 `group.initial.rebalance.delay.ms`의 시간 동안 컨슈머의 요청을 기다립니다.

4. 컨슈머는 컨슈머 등록 요청을 그룹 코디네이터에게 보냅니다. 이때 가장 먼저 요청을 보내는 컨슈머가 컨슈머 그룹의 리더가 됩니다.

5. 컨슈머 등록 요청을 받은 그룹 코디네이터는 해당 컨슈머 그룹이 구독하는 토픽 파티션 리스트 등 리더 컨슈머의 요청에 응답을 보냅니다.

6. 리더 컨슈머는 정해진 컨슈머 파티션 할당 전략에 따라 그룹 내 컨슈머들에게 파티션을 할당한 뒤 그룹 코디네이터에게 전달합니다.

7. 그룹 코디네이터는 해당 정보를 캐시하고 각 그룹 내 컨슈머들에게 성공을 알립니다.

8. 각 컨슈머들은 각자 지정된 토픽 파티션으로부터 메시지들을 가져옵니다.

여기까지가 컨슈머가 어떻게 카프카로부터 메시지를 가져오는지 구체적으로 살펴봤습니다.

이렇게 실제로는 컨슈머 그룹과 그룹 코디테이터가 서로 긴밀하게 내용을 주고 받으며 컨슈머 그룹이 안정적으로 메시지를 읽어갈 수 있도록 유지하기 위해 노력하는 것을 알 수 있었습니다.

카프카 명령어는 아래 링크를 통해 확인이 가능합니다.

[[Kafka 명령어 정리]]