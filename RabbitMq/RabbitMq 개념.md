## 기본 개념

AMQP를 구현한 오픈소스 메세지 브로커
producers에서 consumers로 메세지(요청)를 전달할 때 중간에서 브로커 역할을 한다.

사용하는 케이스
- 요청을 많은 사용자에게 전달할 때
- 요청에 대한 처리시간이 길 때
- 많은 작업이 요청되어 처리를 해야할 때

해당하는 요청을 다른 API에게 위임하고 빠른 응답을 할 때 많이 사용
MQ를 사용하면 애플리케이션간 결합도를 낮출 수 있다는 장점

---

# 중요 개념

![](https://velog.velcdn.com/images/sdb016/post/5763f81e-b710-4a55-9a3e-d7ea16932595/image.png)

- Producer : 요청을 보내는 주체, 보내고자 하는 메세지를 exchange에 publish 한다.
- Consumer : producer로부터 메세지를 받아 처리하는 주체
- Exchange : producer로부터 전달받은 메세지를 어떤 queue로 보낼지 결정하는 장소, 4가지 타입이 있음
- Queue : consumer가 메세지를 consume하기 전까지 보관하는 장소
- Binding : Exchange와 Queue의 관계, 보통 사용자가 특정 exchange가 특정 queue를 binding하도록 정의한다. (fanout 타입은 제외)
- - -
## Binding

Binding은 Exchange와 Queue를 연결하는 관계이다.  
모든 메시지는 Exchange가 가장 먼저 수신하는데 Exchange 타입과 binding 규칙에 따라 적절한 Queue로 전달된다.
- - -
## Exchange 속성

![](https://velog.velcdn.com/images/sdb016/post/712b2677-0fe5-4fbb-a493-811c659ca0be/image.png)

Exchange는 다음과 같은 속성을 가진다.

- Name: Exchange 이름
- Type: 메시지 전달 방식
    - Direct
    - Fanout
    - Topic
    - Headers
- Durability: 브로커가 재시작될 때 남아있는지 여부
    - Durable: 브로커가 재시작되어도 디스크에 저장되어 남아있음
    - Transient: 브로커가 재시작되면 사라짐
- Auto-delete: 마지막 Queue 연결이 해제되면 삭제

- - -

## Queue 속성

![](https://velog.velcdn.com/images/sdb016/post/34a6ba30-d5fc-4599-a5a8-1ec267118afa/image.png)

Queue는 다음과 같은 속성을 가진다.

- Name: Queue 이름, `amq.`은 예약어로써 사용 불가
- Durability: 브로커가 재시작될 때 남아있는지 여부
    - Durable: 브로커가 재시작되어도 디스크에 저장되어 남아있음
    - Transient: 브로커가 재시작되면 사라짐
- Auto delete: 마지막 Consumer가 consume을 끝낼 경우 자동 삭제
- Argument: 메시지 TTL, Max Length 같은 추가 기능 명시

---

## AMQP 수신 확인 모델

AMQP는 네트워크에 문제가 발생하거나 요청을 처리하지 못했을 경우를 대비해 2가지 수신 확인 모델을 가진다.

- Consumer가 메세지를 받으면 브로커에게 통지하고, 브로커는 통지를 받았을 때만 queue에서 해당 메세지 삭제
- 브로커가 메세지를 전달하면 자동으로 삭제

---

## Exchange 4가지 타입


|타입|설명|
|---|---|
|Direct|라우팅 키가 정확히 일치하는 Queue에 메시지 전송|
|Topic|라우팅 키 패턴이 일치하는 Queue에 메시지 전송|
|Headers|[key:value]로 이루어진 header값을 기준으로 일치하는 Queue에 메시지 전송|
|Fanout|해당 Exchange에 등록된 모든 Queue에 메시지 전송|

### Direct Exchange

![](https://velog.velcdn.com/images/sdb016/post/f6438389-fb7e-4475-9538-9804fc334ab5/image.png)

라우팅 키를 이용하여 메시지를 전달할 때 정확히 일치하는 Queue에만 전송한다.  
하나의 Queue에 여러 라우팅 키를 지정할 수 있고, 여러 Queue에 같은 라우팅 키를 지정할 수도 있다.

> Default Exchange는 이름이 없는 Direct Exchange이다. 전달된 목적 Queue 이름과 동일한 라우팅 키를 부여한다.

### Topic Exchange

라우팅 키의 패턴을 이용해 메시지를 라우팅한다.  
여러 Consumer에서 메시지 형태에 따라 선택적으로 수신해야하는 경우 등등 다양한 패턴 구현에 활용될 수 있다.  
![](https://velog.velcdn.com/images/sdb016/post/68553509-3a88-4a18-b173-8f0a195eaf26/image.png)

위 그림의 경우에는 라우팅 키가 정확히 일치하지 않아 binding되지 않은 경우이다.

![](https://velog.velcdn.com/images/sdb016/post/31fbf607-befb-44da-b986-871be08d5f4d/image.png)

위 그림의 경우에는 "animal.rabbit"이 "animal.* "와 "#" 모두에 일치하기 때문에 모든 Queue에 전송되는 경우이다.

> #은 0개 이상의 단어를 대체

### Headers Exchange

![](https://velog.velcdn.com/images/sdb016/post/08a5f77d-12eb-4976-93bc-d8c5065bea4e/image.png)

Topic과 유사한 방법이지만 라우팅을 위해 header를 사용한다는 점에서 차이가 있다.  
producer에서 정의된 header의 key-value 쌍과 consumer에서 정의된 argument의 key-value 쌍이 일치하면 binding된다.  
binding key 만을 사용하는 것보다 더 다양한 속성을 사용할 수 있다.  
이 타입을 사용하면 binding key는 무시되고, 바인딩 시 지정된 값과 헤더 값이 같은 경우에만 일치하는 것으로 간주된다.

`x-match`의 값에 따라 다르게 동작한다.

|key|value|설명|
|---|---|---|
|x-match|all|header의 모든 key-value 쌍 값과 argument의 모든 key-value쌍 값이 일치할 때 binding|
|x-match|any|argument의 key-value 쌍 값 중 하나라도 header의 key-value 쌍 값과 일치할 때 binding|

### Fanout

![](https://velog.velcdn.com/images/sdb016/post/332b753f-d77d-4bc9-bb48-bb0c049295bd/image.png)  
Exchange에 등록된 모든 Queue에 메시지를 전송한다.

---

## Prefetch Count

![](https://velog.velcdn.com/images/sdb016/post/a0cc1cd1-ec45-4d49-95d8-31f1daa2587a/image.png)

하나의 Queue에 여러 Consumer가 존재할 경우, Queue는 기본적으로 Round-Robun 방식으로 메시지를 분배한다.  
이때 예를 들어 홀수 번째 메시지 처리 시간은 짧고, 짝수 번째 메시지 처리 시간이 매우 길 경우, 계속해서 하나의 Consumer만 일을 하게 될 수도 있다.  
이를 예방하기 위해 Prefetch count를 1로 설정하면, 하나의 메시지가 처리되기 전에는 새로운 메시지를 받지 않게 되므로 작업을 분산시킬 수 있다.