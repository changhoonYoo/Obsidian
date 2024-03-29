## **서론**

인터넷은 웹 브라우저와 웹 서버 간의 데이터 통신을 위해서 HTTP 표준 위에 구축되어 있습니다. 대부분의 경우 웹 브라우저인 클라이언트가 HTTP 요청을 서버에 보내고, 서버는 적절한 응답을 반환하는데 이런 왕복 통신은 'https://www.google.com'과 같은 주소를 브라우저에 입력했을 때 웹 페이지를 받게 되는 과정입니다.  
이러한 HTTP 표준은 광범위하게 지원되지만, 애플리케이션이 연속적인 정보를 서버에 전송하거나, 실시간으로 업데이트된 서버의 정보를 클라이언트에게 보내야 하는 경우 지속적인 HTTP 요청을 하게 되기에 비용면에서 매우 비 효율적입니다.  
이런 상황에서 폴링, 웹소켓, 그리고 SSE가 등장했는데, 이들은 데이터 스트림의 속도와 메모리 효율성에 중점을 둔 프로토콜 들입니다.

1. Short Polling
2. Long Polling
3. SSE(Server-Sent-Events)
4. WebSocket

**이번 게시글에서는 위 프로토콜들의 개념에 대해서 간략하게 설명하고, Spring에서 SSE를 활용해 알림 기능을 구현하는 예시를 작성해 보도록 하겠습니다.**  
 

## **1. Short Polling** 

Short Polling은 클라이언트가 서버로부터 정기적인 정보를 받기 위한 프로토콜입니다. 클라이언트가 주기적으로 서버로 요청을 보내 데이터를 받아오는 방법입니다.

![](https://blog.kakaocdn.net/dn/WoVxP/btskk9wmh1h/J7b7c3zuVYJtfI0Hxhnj9K/img.jpg)

Short Polling

과정은 다음과 같습니다.

- 클라이언트가 서버에 새로운 정보에 대한 HTTP 요청을 보낸다.
- 서버는 새로운 정보를 클라이언트에게 반환한다.
- **클라이언트는 설정한 주기(예:2초)로 요청을 반복한다.**

클라이언트가 Http Request를 서버로 계속 보내서 이벤트 내용을 전달받는 방식이며, 클라이언트가 지속적으로 Request를 서버에 보내기 때문에 클라이언트가 많아지면 서버의 부담이 증가하게 됩니다.  
또한 TCP의 connection을 맺고 끊는 것 자체가 매우 무겁고, 이에 따라 실시간으로 변화되는 빠른 정보의 응답을 기대하기는 어렵습니다.  
**하지만 클라이언트와 서버의 구현이 모두 단순하다는 장점이 있고, 서버가 요청에 대한 부담이 크지 않고 요청 주기를 넉넉하게 잡아도 될 정도로 실시간성이 중요하지 않다면 고려해 볼 만한 방법입니다.**  
 

## **2. Long Polling**

Long Polling은 Short Polling 보다는 더 효율적인 방식입니다.

![](https://blog.kakaocdn.net/dn/F0bKq/btskgun42zg/sDX0KQIfbYVpsL008afnzK/img.jpg)

Long Polling

Short Polling 보다 서버에서 기다리는 시간이 더 길어진 것이 보이시나요? 과정은 Short Polling에서 마지막만 다릅니다.

- 클라이언트가 서버에 새로운 정보에 대한 HTTP 요청을 보낸다.
- 서버는 새로운 정보를 클라이언트에게 반환한다.
- **클라이언트는 이전 응답을 받자마자 다시 요청을 반복한다.**

Long Polling은 Short Polling에 비해 동일한 양의 데이터를 클라이언트에게 전송하는데 필요한 HTTP 요청 수를 줄일 수 있습니다.   
하지만 서버로부터 응답을 받고 나면 다시 연결 요청을 하기 때문에, 상태가 빈번하게 바뀐다면 연결 요청도 늘어나 서버에 부담이 가는 것은 변하지 않습니다.  
**따라서 실시간 메시지 전달이 중요하지만 서버의 상태가 빈번하게 변하지 않는 경우에 적합합니다.**  
 

## **3.** **SSE(Server-Sent-Events)**

SSE는 서버와 한번 연결을 맺고 나면, 일정 시간 동안 서버에서 변경이 발생할 때마다 서버에서 클라이언트로 데이터를 전송하는 방법입니다.

![](https://blog.kakaocdn.net/dn/5tDxa/btskgH80bNN/SQS5K1NkkMjoePk5L3n77k/img.jpg)

SSE(Server-Sent-Events)

과정은 다음과 같습니다.

- 클라이언트는 서버를 구독한다.(SSE Connection을 맺는다.)
- **서버는 변동사항이 생길 때마다 구독한 클라이언트들에게 데이터를 전송한다.**

SSE는 상황에 따라서 응답마다 다시 요청을 해야 하는 Long Polling 방식보다 효율적입니다. SSE는 서버에서 클라이언트로 text message를 보내는 브라우저 기반 웹 애플리케이션 기술이며 HTTP의 persistent connections을 기반으로 하는 HTML5 표준 기술입니다.  
하지만 HTTP를 통한 SSE(HTTP/2가 아닐 경우)는 브라우저 당 6개의 연결로 제한되므로, 사용자가 웹 사이트의 여러 탭을 열면 첫 6개의 탭 이후에는 SSE가 작동하지 않는다는 단점도 있긴 합니다. (HTTP/2에서는 100개까지의 접속을 허용합니다.)  
하지만 **전반적으로 SSE는 클라이언트가 서버와 크게 통신할 필요 없이 단지 업데이트된 데이터만 받아야 하는 실시간 데이터 스트림에 대한 구현이 필요할 때는 매우 훌륭한 선택입니다.**  
 

## **4. WebSockets**

WebSockets은 OSI 네트워크 모델의 4 계층 프로토콜인 TCP에 기반한 양방향 메시지 전달 프로토콜입니다. WebSockets은 프로토콜 오버헤드가 적고, 네트워크 스택에서 더 낮은 수준에서 동작하기 때문에 HTTP보다 데이터 전송이 빠릅니다.

![](https://blog.kakaocdn.net/dn/kZtLa/btsklnIa0K6/Uklunoqz3GIgCOlMhmAIHK/img.jpg)

WebSockets

상위 수준에서 보면 WebSockets의 과정은 다음과 같습니다.

- 클라이언트와 서버는 HTTP를 통해 연결을 설정하고, WebSockets 핸드셰이크를 통해 연결한다.
- **WebSockets의 데이터는 클라이언트 서버와 양방향으로 전송된다.**

웹 소켓의 가장 큰 장점은 속도입니다. 클라이언트와 서버는 메시지를 전송할 때마다 서로의 연결을 찾아서 다시 설정할 필요가 없습니다. 웹 소켓 연결이 설정되면 데이터는 어느 방향으로든 즉시 안전하게 전송될 수 있습니다.(TCP이기에 메시지가 항상 순서대로 도착하도록 보장됨)  
하지만 단점은 초기 구현에 상당히 많은 비용이 들어간다는 점입니다. 웹소켓은 멀티 온라인 게임과 같은 실시간 상태 업데이트와 긴밀한 동기화를 통해 분산된 사용자에게 전송해야 하는 경우 유용합니다.  
따라서 **전반적으로 웹 소켓은 빠른 고품질의 양방향 연결이 필요하다면 좋은 선택이지만, 시스템에 상당한 복잡성을 추가하고 구현하는 데 많은 투자가 필요하므로 폴링이나 SSE가 적합하지 않은 경우에만 사용하는 것을 권장합니다.**

- - -

`Map<String, SseEmitter>`과 `CopyOnWriteArrayList<SseEmitter>`은 두 가지 다른 자료 구조로, 각각의 사용 사례에 맞게 선택되어야 합니다.


###  ConcurrentHashMap<>() & CopyOnWriteArrayList<>()

```java
private final Map<String, SseEmitter> emitters = new ConcurrentHashMap<>(); private final CopyOnWriteArrayList<SseEmitter> emitters = 
new CopyOnWriteArrayList<>();
```
1. **Map<String, SseEmitter>:**
    
    - `Map`을 사용하면 각 `SseEmitter`에 고유한 식별자(예: 사용자 ID)를 매핑할 수 있습니다.
    - 특정 사용자에게 직접 이벤트를 전송할 때 유용합니다.
    - 매핑된 식별자를 사용하여 특정 사용자의 `SseEmitter`를 찾아 알림을 전송할 수 있습니다.
    
    javaCopy code
    
    `private final Map<String, SseEmitter> emitters = new ConcurrentHashMap<>();  public void addUserEmitter(String userId, SseEmitter emitter) {     emitters.put(userId, emitter); }  public void sendToUser(String userId, String message) {     SseEmitter emitter = emitters.get(userId);     if (emitter != null) {         try {             emitter.send(SseEmitter.event().data(message));         } catch (IOException e) {             // 에러 처리         }     } }`
    
2. **CopyOnWriteArrayList<SseEmitter>:**
    
    - `CopyOnWriteArrayList`은 리스트를 수정할 때 복사를 수행하므로, 동시성 문제에서 안전합니다.
    - 모든 연결된 클라이언트에게 이벤트를 브로드캐스트하는 데 적합합니다.
    - 간단하게 모든 클라이언트에게 일괄적으로 메시지를 보낼 때 사용할 수 있습니다.
    
    javaCopy code
    
    `private final CopyOnWriteArrayList<SseEmitter> emitters = new CopyOnWriteArrayList<>();  public void addEmitter(SseEmitter emitter) {     emitters.add(emitter); }  public void broadcast(String message) {     for (SseEmitter emitter : emitters) {         try {             emitter.send(SseEmitter.event().data(message));         } catch (IOException e) {             // 에러 처리         }     } }`
    

**차이점 요약:**

- `Map`은 특정 키(사용자 ID 등)에 연결된 `SseEmitter`를 관리하는 데 유용합니다.
- `CopyOnWriteArrayList`은 여러 클라이언트에게 브로드캐스트할 때 유용합니다.
- 사용 사례에 따라 적절한 자료 구조를 선택하세요.