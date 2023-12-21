JWT(Json Web Token)은 위와 같은 일련의 과정속에서 나타난 하나의 `인터넷 표준 인증 방식`입니다. 말그대로 인증에 필요한 정보들을 Token에 담아 암호화시켜 사용하는 토큰인 것이죠.

따라서 사실 기본적인 인증을 진행하는 구조는 Cookie때와 크게 다르지는 않습니다. 다만, 강조되는 점은 JWT는 **서명된 토큰**이라는 점입니다. 공개/개인 키를 쌍으로 사용하여 토큰에 서명할 경우 서명된 토큰은 개인 키를 보유한 서버가 이 서명된 토큰이 정상적인 토큰인지 인증할 수 있다는 이야기이죠.

이러한 JWT의 구조 때문에 인증 정보를 담아 안전하게 인증을 시도하게끔 전달할 수 있는 것입니다.

그럼 구조가 어떻게 되어있는지 확인해보겠습니다.

JWT는 각각의 구성요소가 점(`.`)으로 구분이 되어있으며 구성 요소는 다음과 같습니다.

- Header
    
- Payload
    
- Signature
    
    아래처럼 각각의 구성 요소가 .으로 구분되어있는 형태이죠.
    
    `xxxxx.yyyyy.zzzzz`
    
    그럼 지금부터는 상세하게 각 구성요소에 어떤 값이 들어있는지 확인해보도록 하겠습니다.
    

### 1. Header

```json
{
  "typ": "JWT",
  "alg": "HS512"
}
```

header에는 보통 토큰의 타입이나, 서명 생성에 어떤 알고리즘이 사용되었는지 저장합니다.

지금같은 경우에는 현재 토큰의 타입이 `JWT`이고, 앞서 이야기했던 개인키로 `HS512` 알고리즘이 적용되어 암호화가 되어있다고 확인할 수 있겠네요 😀

### 2. Payload

```json
{
  "sub": "1",
  "iss": "ori",
  "exp": 1636989718,
  "iat": 1636987918
}
```

payload에는 보통 **Claim**이라는 사용자에 대한, 혹은 토큰에 대한 property를 **key-value**의 형태로 저장합니다. Claim이라는 말 그대로 토큰에서 사용할 정보의 조각인 셈이죠.

사실 어떤 Claim값을 넣는지는 개발자의 마음(?)이긴 하지만 일단은 배울 때는 JWT의 표준 스펙을 알아보도록 할게요.

표준 스펙상 key의 이름은 3글자로 되어있습니다. JWT의 핵심 목표는 사용자에 대한, 토큰에 대한 표현을 압축하는 것이기 때문에 이를 정의한 것이라고 볼 수 있겠네요.

1. `iss` (Issuer) : 토큰 발급자
2. `sub` (Subject) : 토큰 제목 - 토큰에서 사용자에 대한 식별값이 됨
3. `aud` (Audience) : 토큰 대상자
4. `exp` (Expiration Time) : 토큰 만료 시간
5. `nbf` (Not Before) : 토큰 활성 날짜 (이 날짜 이전의 토큰은 활성화 되지 않음을 보장)
6. `iat` (Issued At) : 토큰 발급 시간
7. `jti` (JWT Id) : JWT 토큰 식별자 (issuer가 여러명일 때 이를 구분하기 위한 값)

이러한 표준 스펙으로 정의되어있는 Claim 스펙이 있다는 것이지, 꼭 이 7가지를 모두 포함해야하는 것은 아니고, 상황에 따라 해당 서버가 가져야할 인증 체계에 따라 사용하면 됩니다.

표준 스펙 외에도 필요하다 싶으면 추가해도 전혀 문제가 없습니다. 그건 개발자의 자유니까요. 예를 들어 뒤에서 이야기하겠지만 Access Token과 Refresh Token을 구분하고 싶다면 "token_type"라는 커스텀한 Claim을 만들고 값을 아래와 같이 "access token"으로 두어 구분지어도 괜찮습니다.

```json
{
  "token_type" : "access token"
}
```

다만 가장 중요한 것은 payload에 **민감한 정보를 담지않는 것**입니다. 위에 header와 payload는 json이 디코딩되어있을 뿐이지 특별한 암호화가 걸려있는 것이 아니기 때문에 누구나 jwt를 가지고 디코딩을 한다면 header나 payload에 담긴 값을 알 수 있기 때문이죠.

아래의 디코딩 과정을 보면,

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fd50f302f-c458-422c-bc8d-441743d16c7b%2F4.png)

[jwt.io](https://jwt.io/)에서 서버에서 생성한 JWT를 넣기만해도 볼 수 있는 화면입니다. JWT에서 header와 payload는 특별한 암호화없이 흔히 사용할 수 있는 base64 인코딩을 사용하기 때문에 서버가 아니더라도 그 값들을 확인할 수 있습니다.

그래서 JWT는 단순히 "식별을 하기위한" 정보만을 담아두어야하는 것입니다.

> 간혹 JWT의 header나 payload가 식별값만 존재하지만 해당 값들도 암호화를 통해 감추어야하지 않느냐는 생각을 가졌을 때가 있습니다.
> 
> 예를 들어 base64UrlEncode(암호화(header)).base64UrlEncode(암호화(payload)).signature(암호화된 이 둘을 또 암호화)와 같은 형태였습니다.
> 
> 제가 생각했던 방법이지만 사실 필요없는 행위입니다. 암호화는 민감한 정보를 막아두어야할 때는 필요한 행위이지만 이 자체만으로도 많은 리소스를 사용하기 때문에 신중하게 사용해야합니다. 매 http 요청마다 한번의 복호화가 더 추가되는 셈이니까요.
> 
> 그렇기 때문에 유출되었을 때 그렇게 큰 상관이 없는 비민감정보를 토큰에 담는 것이 기본 스펙이 되는 것이고, 서버는 굳이 header나 payload를 암호화하지 않아도 되는 것입니다.

### 3. Signature

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fa28efde0-67d7-4a06-adbc-8bc11ad89086%2F5.png)

가장 중요한 서명입니다. 물론 암호화가 되어있기 때문에 그 구조에 대한 이미지를 가져왔어요.

지금까지 Header와 Payload를 보여줄 때는 인코딩 되어있던 값들을 JWT에 담겨있는 것처럼 디코딩된 상태를 사용합니다. header를 디코딩한 값, payload를 디코딩한 값을 위처럼 합치고 이를 `your-256-bit-secret` 즉, 서버가 가지고 있는 개인키를 가지고 암호화되어있는 상태입니다.

따라서 signature는 서버에 있는 개인키로만 암호화를 풀 수 있으므로 다른 클라이언트는 임의로 Signature를 복호화할 수 없습니다.

잘 생각해보면 위에 설명한 내용만 봐도 어떻게 복호화해서 인증하는지는 쉽게(?) 알 수 있을 것입니다.

1. JWT 토큰을 클라이언트가 서버로 요청과 동시에 전달한다.
2. 서버가 가지고있는 개인키를 가지고 Signature를 복호화한 다음 `base64UrlEncode(header)`가 JWT의 heaer값과 일치한지, `base64UrlEncode(payload)` 와 일치한지 확인하여 일치한다면 인증을 허용합니다.

만약 클라이언트가 payload에 담긴 식별자가 변조된 JWT로 요청을 하더라도 서버가 애초에 발급했던 Signature 안의 payload와 다르기 때문에 인증이 불가능해지겠죠.

### 장점

지금까지 쿠키와 세션을 넘어오면서 겪었던 모든 단점을 해결하는 것이 JWT인 것이기 때문에 단점들을 뒤집으면 JWT의 장점이라고 볼 수 있을 것 같습니다.

1. 이미 토큰 자체가 인증된 정보이기 때문에 세션 저장소와 같은 별도의 인증 저장소가 "필수적"으로 필요하지 않습니다.
2. 세션과는 다르게 클라이언트의 상태를 서버가 저장해두지 않아도 됩니다.
3. signature를 공통키 개인키 암호화를 통해 막아두었기 때문에 데이터에 대한 보완성이 늘어납니다.
4. 다른 서비스에 이용할 수 있는 공통적인 스펙으로써 사용할 수 있습니다.

처음 글부터 지금까지의 내용을 한 문장으로 요약하자면

> stateful해야하는 세션의 단점을 보완하기 위해 만들어진 JWT는 별도의 세션 저장소를 강제하지 않기 때문에 stateless하여 확장성이 뛰어나고, signature를 통한 보안성까지 갖추고있다.

라고 이야기할 수 있겠네요.

## Spring에서의 JWT 맛보기

코드 예시를 보면서 조금 맛볼까합니다. 사이드 프로젝트에서 간단하게 JWT를 사용한 예제이니 이렇게 진행이 되는구나 하고 이해하는 용도로 봐주시면 좋을 것 같아요 :)

### 1. JWT 발급받기

먼저 Login를 살펴보면 Login에 필요한 ID, PW를 확인하는 로직이 필요하겠죠. 아래는 그걸 위한 `UserService` 내의 하나의 로직입니다.

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F7b6d8439-b7df-475a-9188-fd59736ee740%2F6.png)  
![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F695757f2-cbb5-4932-9e81-75df4f6c258d%2F7.png)  
![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fdeb89d31-01a2-4cde-99ea-882fb1e9a5c1%2F8.png)

Email에 해당하는 User를 찾고, user 객체가 password와 passwordEncode를 가지고 로그인이 되었는지 확인하는 로직입니다.

만약 Email에 해당하는 유저가 없으면 ErrorCode.USER_NOT_FOUND exception이 발생할 것이고 password가 다르다면 ErrorCode.PASSWORD_DISMATCH exception이 발생하겠네요.

그럼 이 Service로직을 불러오는 API 로직을 확인해보면,

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fcfadedde-a1bf-461b-9ea5-3ddeca0ce789%2F9.png)

Request에 로그인 객체를 받아서 위의 로그인로직을 불러오고, 반환값으로 로그인한 user객체와 무언가 create한 객체를 response에 바인딩해서 내보냅니다.

지금 나오는 `jwtService.createToken(user)`가 로그인 요청을 한 클라이언트가 앞으로 사용할 인증키, JWT가 됩니다. 내부 구현체를 한번 살펴보면 아래와 같습니다.(물론 다른부분들은 생략되어있는 것이 많습니다.)

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fb1ecb804-1741-4f1c-9d41-55ca3ae46e9f%2F10.png)

JWT를 생성하기 위해서 jjwt라는 라이브러리를 사용을 한 모습입니다.

- signWith : 위에서 이야기한 개인키를 가지고 HS512 암호화 알고리즘으로 header와 payload로 Signature를 생성.
- setHeaderParam : header에 typ로 JWT 지정
- setSubject : 이후 JWT로 인증할 식별자를 user 객체의 id로 지정
- setIssuer : JWT 발급자 지정
- setExpiration : 현재 시간으로부터 지정된 accessTime만큼의 만료시간 지정
- setIssueAt : JWT를 발급한 시간 지정

메소드를 통해서 반환값은 말한대로 header, payload, signature로 구성된 즉, `xxx.yyy.zzz`의 형태를 띄는 JWT가 생성이 될 것입니다. API호출을 해보면 아래와 같은 Response를 받아볼 수 있겠네요.

```java
{
  "user": {
    "username": "JinyoungChoi",
    "email": "jinyounchoi95@gmail.com",
    "bio": null,
    "image": null,
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzUxMiJ9.eyJzdWIiOiIxIiwiaXNzIjoib3JpIiwiZXhwIjoxNjM2OTg5NzE4LCJpYXQiOjE2MzY5ODc5MTh9.FwCSkmdm5w1E6IrbdOpAaVVwAuSWGyfkQfelIh2SxlvHmdTJPjS6KmdTokCb7H7_WM1qepvFG27XyxCLwHcM8A"
  }
}
```

token을 한번 디코딩해보면,

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F500d32a7-440a-45a8-8ccc-8eba05e196d2%2F11.png)

tye이 JWT이고, alg는 HS512를 사용했고, 토큰 발급자는 ori이고 .... 계속 이야기했던 내용들의 연속이니 바로 이해하실것이라고 생각합니다.

### 2. JWT로 인증이 필요한 API 요청해보기

이제 요청에 사용할 Token을 받았으니 이걸 가지고 인증이 필요한 API를 call해보도록 하겠습니다.

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F50f7aade-026b-43fc-8a10-d8fb5c6d57cd%2F12.png)

현재 로그인한 유저의 정보를 받아오는 API로 별다른 로직은 없고 `userRepository.findById(id)` 를 해서 현재 유저정보를 반환한다~고만 생각해주시면 좋을 것 같습니다.

문제는 Security를 사용한다고 했을 때 이 로직에 유저 권한이 있을 때만 접근하도록 제한되어있다면, 어떠한 작업이 필요한 것인데요. 따라서 인증된 유저가 API를 호출하는 것이 아니라면, 접근 에러가 날 것이며 또한 인증된 사람이 없으니 `@CurrentUserId`도 당연히 null이 들어올 것입니다.

세세한 이야기는 security의 구조를 이야기해야하는 부분이기 때문에 넘어가기로하고 시큐리티가 JWT를 확인하는 부분만 알아보도록 할게요. 아래 코드는 JWT를 확인하기 위해서 커스텀한 인증 Filter의 일부입니다.

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2Fad76bc4e-73e2-4df8-bca7-783cd2fb8b60%2F13.png)

조금 복잡하지만 차근차근 알아보면

1. resolveToken()을 통해서 헤더에 `AUTHORIZATION`으로 지정된 헤더값을 가져온다.
2. 가져온 헤더값이 지정된 형태를 띄고있는지 checkMatch()를 통해서 확인하고 온전한 JWT 값만 꺼내온다.
3. token값이 있다면 `jwtService.getUser(token)`을 통해 토큰을 resolving하고, 식별값을 가져온다.
4. 이를 Security가 Filtering할 수 있도록 SecurityContext holder에 넣어두고 다음 작업으로 진행한다.

1부터 살펴보면 JWT를 사용할 때는 헤더에 `AUTHORIZATION` 으로 헤더값을 전달합니다. JWT는 데이터량이 많기 때문에 특정 헤더에 값을 넣어 전달해야하고 그 약속이 `AUTHORIZATION` 헤더값인 거죠.

2는 JWT의 공식적인 스펙에 대한 이야기입니다. 물론 JWT의 코어는 `xxx.yyy.zzz` 의 형태로 되어있지만 JWT 토큰 혹은 지금은 배우지 않았지만 OAuth에 대한 토큰은 Bearer인증 타입의 스펙이기 때문에 `bearer xxx.yyy.zzz` 의 형태로 반환을 해야합니다. 이렇게 하지 않으면 JWT를 못쓰는 것이 아닌 bearer인증 타입의 공식적인 스펙, 즉 약속이기 때문에 이와같은 형태를 지켜서 생성하는 것이 좋습니다.

3은 `jwtService.getUser()`메소드를 보면서 이야기하겠습니다.

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F313d1829-a1c0-4f86-ac0f-c06a7aeafed4%2F14.png)

많아보이지만 사실은 앞서 이야기한 jjwt 라이브러리와 개인키를 이용해서 signature를 복호화하는 과정입니다. `setSigningKey(key)` 가 개인키를 가지고 복호화를 하는 내용에 속하겠네요. 이후 Claims 중 인증된 유저의 식별값으로 앞서 넣었던 `sub` 를 반환합니다.

만약 JWT에 조작이 되어 Signature와 동일한 값이 들어가있지 않다면 혹은 시간이 만료되거나 등의 상황이 발생한다면 위의 다양한 Exception에 catch되어 예외가 발생하게 될 것입니다.

4는 Security를 많이 하셨다면 자주보셨을 내용이며 인증된 값을 가지고 SecurityContextHolder에 저장을 하면 해당 값을 가지고 Security는 인증을 허가하며, 내부에 있는 인증 객체를 가지고 인가를 하거나 현재는 커스텀되어있는 `@CurrentUserId` 를 통해 controller가 인증된 값을 파라메터로 가져올 수 있는 등과 같은 작업을 진행할 수 있습니다.

이후에는 인증된 사용자가 API를 사용하는 과정이므로 생략하도록하겠습니다.

설명만 들어서는 조금 까다로워보일 수는 있지만 JWT에 해당하는 내용 자체는 지금까지 이야기했던 개념을 그대로 코드로 옮겨놓은 부분이기 때문에 이해하는데 큰 어려움이 되지 않으셨을거라 생각합니다.

세부적인 인증방법이나 상황에 따라 다르겠지만 처음 이야기한대로 JWT만을 쉽게 사용하기 위해 만든 예제이므로 아 이렇게 동작을 하는구나 하고 넘어가주시면 좋을 것 같습니다 :)

혹시나 예제 코드가 필요하신분이 있을 수 있어 스터디에서 제가 공유했던 [JWT 가이드 코드](https://github.com/real-world-study/realworld/pull/34/files)만 링크 남겨두겠습니다. 지금과 구조가 조금 달라 참고용으로만 봐주세요 🙇‍♂️

## JWT에도 한계점은 있습니다.

어떤 개발 스펙도 항상 100%를 만족시켜주는 것은 없다고 생각합니다. 그것은 JWT에도 마찬가지라고 생각해요.

저 좋은 장점도 있지만 JWT를 가만히 살펴보면 다음과 같은 단점도 확인할 수 있습니다.

### 단점

- 쿠키, 세션때와는 다르게 base64인코딩을 통한 정보를 전달하므로 전달량이 많다. 따라서 네트워크 전달 시 많은 데이터량으로 부하가 생길 수 있다.
- Payload에는 암호화가 되어있지 않기 때문에 민감 정보를 저장할 수 없다.
- 토큰이 탈취당하면 만료될 때까지 대처가 불가능하다.

단점의 첫번째와 두번째는 크리티컬한 문제는 아니지만 3번째 단점이 핵심입니다.

> **토큰이 탈취당하면 만료될 때까지 대처가 불가능하다.**

세션을 이야기할 때는 세션을 탈취당한다고 판단이 되었을 때 세션저장소를 끊어서 탈취당한 세션 ID가 있더라도 세션저장소에 그 값을 지워 탈취된 후의 상황을 보완할 수 있기는 했습니다. 근데 이건 서버에서 클라이언트의 상태를 저장하는 stateful한 상황이고 지금은 stateless 해요.

애초에 JWT를 발급해서 전달하고, 그 이후의 상황은 클라이언트가 관리하는 형태로 체계가 잡혀있기 때문에 서버가 탈취의 상황이 판단이 되어도 관리할 수 있는 방법이 없는거죠. 그럼 이걸 어떻게 해결할까요?

### exp를 짧게 가져가자

JWT의 스펙에서 알아보았던 `exp`, **Expiration Time(만료시간)**을 짧게가져가면 됩니다.

물론 쿠키나 세션도 회사마다 만료시간을 두어 강제로 invalidate하게 두는 경우도 있기는합니다. JWT도 탈취되었을 경우의 안정성 때문에 만료시간을 짧게가져가기도 해요. 30분 혹은 1시간 정도의 짧은 토큰을 저장한다면, 만약 토큰이 탈취되어서 서버가 이를 강제로 끊지는 못하겠지만 짧은 시간만 유효하기 때문에 최소한의 보안성을 보장할 수 있겠죠.

하지만, 항상 서비스를 사용하는 주체는 사용자이기 때문에 사용자의 입장에서 생각을 해봐야합니다. 사용자 입장에서는 30분, 1시간만 유효하기 때문에 1시간 뒤에는 ID, PW를 또 입력해야하는 불편한 경험을 느낄 수 밖에 없거든요.

따라서 이 짧은 유효시간을 보완할 만한 두가지 방법 또한 존재하기는 합니다.

## 짧은 JWT 유효시간을 보완하는 방법들

맹점은 서버가 JWT의 만료되기 직전의 유효시간을 보고 재발급을 어떤 방식으로 하느냐에 따라서 갈려요. 다양한 방법들이 있겠지만 대표적인 두가지 방법을 이야기하고자 합니다.

(앞으로는 지금까지 이야기했던 처음 발급한 JWT, 즉 access하기 위한 토큰은 `Access Token` 이라고 이야기합니다.)

1. Sliding Session
2. Refresh Token

### 1. Sliding Session

특정한 서비스를 계속 사용하고 있는 특정 유저에 대해 만료 시간을 연장 시켜주는 방법입니다.

어떤 글을 지속적으로 쓰고 있는 유저가 짧은 만료시간 때문에 글을 쓰다가 인증이 취소되어 글이 날아가면 아까 이야기한 불편한 경험을 제공하게 될 것입니다. Sliding Session을 사용하게 된다면, 글쓰기, 결제 등과 같은 특정 action을 유저가 행동하였을 때 새롭게 만료시간을 늘린 JWT를 다시 제공함으로써 만료시간을 연장하여 보완하는 방법입니다.

다만, 접속이 단발성으로 일어난다면 Sliding Session으로 연장시켜줄 수 없는 상황이 생기고, 너무 긴 Access Token을 발급시켜준 상황이라면 Sliding Session때문에 무한정 사용하는 상황이 발생할 수 있습니다.

### 2. Refresh Token

가장 많이 사용하는 방법으로 JWT를 처음 발급할 때 Access Token과 함께 Refresh Token이라는 토큰을 발급하여 짧은 만료시간을 해결하는 방법입니다.

짧은 시간이 아닌 비교적 긴시간 (7일, 30일 등)의 만료시간을 가진 Refresh Token은 말그대로 Access Token을 Refresh해주는 것을 보장하는 토큰입니다. 만약 클라이언트가 Access Token이 만료됨을 본인이 인지하거나, 서버로부터 만료됨을 확인받았다면 Refresh Token으로 서버에게 새로운 Access Token을 발급하도록 요청하여 발급받는 방식입니다.

한가지 시나리오를 들어서 구체적으로 어떻게 Refresh Token이 동작하는지 알아보도록 할게요.

1. 클라이언트가 ID, PW로 서버에게 인증을 요청하고 서버는 이를 확인하여 Access Token과 Refresh Token을 발급합니다.
2. 클라이언트는 이를 받아 Refresh Token를 본인이 잘 저장하고 Access Token을 가지고 서버에 자유롭게 요청합니다.
3. 요청을 하던 도중 Access Token이 만료되어 더이상 사용할 수 없다는 오류를 서버로부터 전달 받습니다.
4. 클라이언트는 본인이 사용한 Access Token이 만료되었다는 사실을 인지하고 본인이 가지고 있던 Refresh Token를 서버로 전달하여 새로운 Access Token의 발급을 요청합니다.
5. 서버는 Refresh Token을 받아 서버의 Refresh Token Storage에 해당 토큰이 있는지 확인하고, 있다면 Access Token을 생성하여 전달합니다.
6. 이후 2로 돌아가서 동일한 작업을 진행합니다.

시나리오를 보면 새로운 저장소가 하나 나옵니다. **Refresh Token Storage**. 서버에서 Refresh Token을 저장하는 저장소인데요. 어떻게 보면 세션 저장소와 똑같은 역할을 한다고 생각하면 좋을 것 같습니다. 만료된 Access Token은 더이상 인증의 역할을 못하니 Refresh Token을 서버로 전달하고, 이 Refresh Token이 서버의 token 저장소에 있는지 확인 후 Access Token을 발급하는 형태인데요.

사실상 세션과 별반 차이없이 특정 Storage에 I/O작업이 발생하게 되기 때문에 Access Token이 지속되는 짧은 시간동안만 I/O작업이 일어나지 않는다이지, 세션의 단점을 하나 가져가는 셈이 됩니다.

다만 이를 사용함으로써 세션처럼 토큰 자체가 탈취되었다고 판단이 뒤면 Refresh Token Storage를 초기화하여 탈취된 토큰이 더 Refresh 못하도록 막는 등과 같은 부가 옵션이 생깁니다.

> 하지만 세션의 I/O 작업에 대한 단점을 다 가져가니까 어짜피 세션이랑 똑같은거 아니야? 라는 생각을 가지면 안됩니다.
> 
> 세션은 항상 인증 요청을 할 때마다 세션 ID를 세션 저장소에 있는 세션 ID에 비교를 해야해요. 예를 들어 30분에 1만번의 요청을 한다고 했을 때 I/O는 1만번 작동하겠죠.
> 
> 근데 Refresh Token은 Refresh 하는 그 순간 즉, 요청을 했는데 access token이 만료되었을 때만 I/O작업을 해요. 30분에 1만번의 요청을 한다고 했을 때 1만번 access token으로 I/O 작업 없이 요청을 주고받고 30분이 지나면 딱한번 Refresh 하기 위해서 요청을 하는 셈이죠.

## 📄결론

지금까지 인증 체계에서 JWT가 등장할 수 밖에 없었던 배경과, JWT에 대한 구체적인 이야기, JWT의 단점을 보완할 수 있는 방법들에 대해서 이야기 했습니다.

현재 대부분의 서비스가 REST API를 사용하는 시점에서 Stateless한 방법을 사용하기 위해서는 어쩔 수 없이 지금과 같은 토큰의 방식의 인증 체계를 사용할 수 밖에 없기는 합니다. 쿠키는 보안상 문제가 너무 많고, 세션은 요청을 할 때마다 필수적으로 I/O작업이 일어나야하니까요.

물론 JWT 조차도 완전하지는 않아서 위처럼 Sliding Session을 사용하거나 Refresh Token을 사용해야하는 부수적인 요인이 있지만 추가적으로 작업을 요하는 내용들이라 100% 완벽하다고 말할 수는 없는 것 같습니다.

여담으로 굳이 Refresh Token에 너무 얽매이지 않아도 된다고 생각합니다.

![](https://velog.velcdn.com/images%2Fjinyoungchoi95%2Fpost%2F3cb7216b-d0b7-4f11-942c-264f42b0e37e%2F20.png)

위는 GitHub에서의 access token인데요, 만료시간 없는 access token을 제공하는 것을 볼 수 있다시피 만료 자체가 없는 토큰을 제공하기도 하거든요.

가장 중요한 것은 개발하는 서비스의 성격에 맞게, 보안과 사용자 편의를 비교해가면서 주어진 상황에 주어진 좋은 인증 정책을 결정하여야 합니다.