#### 설명
타임리프는 컨트롤러가 전달하는 데이터를 이용해 동적으로 화면을 만들어주는 역할을 하는 뷰 템플릿 엔진이다.
- 서버상에서 동작하지 않아도 HTML 파일의 내용을 바로 확인이 가능하다.
- **순수 HTML 구조를 유지**한다.

#### 특징
타임리프는 화면 구성을 서버 가동없이 쉽게 파악할 수 있어 개발에 수정할 때마다 서버 재가동이 필요 없어지기 때문에 개발에 용이해진다.
서버 가동없이 웹 브라우저를 이용해 해당 파일을 열게 되면 웹 브라우저는 타임리프가 사용하는 속성인 **th:속성**을 알지 못하기 때문에 **타임리프 속성을 제외한 순수 HTML 속성으로 화면을 구성**하여 순수 HTML파일을 유지할 수 있다. 반면에 서버가 가동이 되어 타임리프 뷰 템플릿 엔진을 이용하게 되면 서버 사이드에서 렌더링 되어 **기존 HTML의 속성이 아닌 타임리프의 속성으로 대체되어 동적 HTML 파일을 제공**할 수 있게된다.

#### 설정
######  **Properties**
```properties
# Thymeleaf  
spring.thymeleaf.prefix=classpath:/templates/  
spring.thymeleaf.suffix=.html  
spring.thymeleaf.cache=false  
spring.thymeleaf.mode=HTML  
spring.thymeleaf.encoding=UTF-8
```
###### **Maven**
```xml
<!--Thymeleaf-->  
<dependency>  
   <groupId>org.springframework.boot</groupId>  
   <artifactId>spring-boot-starter-thymeleaf</artifactId>  
</dependency>
```


#### 문법

타임리프를 사용하기 위해서는 html 태그에 다음과 같이 추가해준다.
```html
<html xmlns:th="http://www.thymeleaf.org">
```


타임리프는 주로 **HTML 태그에 th:* 속성을 지정하는 방식으로 동작**합니다.

th:* 로 속성을 적용하면 기존 HTML 속성을 대체하고, 기존 속성이 없으면 새로 만듭니다.

```html
<h1>속성 설정</h1><input type="text" name="mock" th:name="userA" />
```

위 처럼 th:* 속성을 지정하면 타임리프는 name으로 중복되는 기존 속성인 HTML의 name="mock"이 아닌 th:name="userA"로 속성을 대체합니다.

> **속성 추가**

```html
<h1>속성 추가</h1>
- th:attrappend = <input type="text" class="text" th:attrappend="class='large'" /><br/>
- th:attrprepend = <input type="text" class="text" th:attrprepend="class='large '"/<br/>
- th:classappend = <input type="text" class="text" th:classappend="large" /><br/>
```

- th:attrappend => 속성 값의 뒤에 값을 추가
- th:attrprepend => 속성 값의 앞에 값을 추가
- th:classappend => class 속성에 자연스럽게 추가 (띄어 쓰기 까지 적절하게 사용해 붙여줌)

![etc-image-0](https://blog.kakaocdn.net/dn/cFmNRQ/btr7gSuU25Y/hG6qLXKIF0QlXHePPy6Vk0/img.png)

---

##### 텍스트 출력

타임리프는 기본적으로 HTML 테그의 속성에 기능을 정의해서 동작합니다.

HTML의 콘텐츠에 데이터를 출력할 때는 다음과 같이 th:text="${...}" 를 사용하고,

 HTML 테그의 속성이 아니라 HTML 콘텐츠 영역안에서 직접 데이터를 출력하고 싶으면 다음과 같이 \[\[...\]\] 를 사용하면 됩니다.

```html
<h1>컨텐츠에 데이터 출력하기</h1>
<ul>
	<li>th:text 사용 = <span th:text="${data}"></span></li>
	<li>컨텐츠 안에서 직접 출력 = [[${data}]]</li>
</ul>
```

![etc-image-1](https://blog.kakaocdn.net/dn/QcHrg/btr7rtt2D8U/Olk1h7pqekU5oNsTO8kIL1/img.png)

![etc-image-2](https://blog.kakaocdn.net/dn/cCKMxf/btr7rumbJ3X/6lYhoHviEzWOMgVhM2cOjK/img.png)

> **Unescape**

HTML 문서는 < , > 같은 특수 문자를 기반으로 정의되기 때문에 뷰 템플릿으로 HTML 화면을 생성할 때는 출력하는 데이터에 이러한 특수 문자가 있는 것을 주의해서 사용해야 합니다.

웹 브라우저는 < 를 HTML 테그의 시작으로 인식해서 < 를 테그의 시작이 아니라 문자로 표현할 수 있는 방법이 필요한데, 이것을 HTML 엔티티라 합니다.

그리고 이렇게 HTML에서 사용하는 특수 문자를 HTML 엔티티로 변경하는 것을 이스케이프(escape)라 합니다.

```java
@GetMapping("text-unescaped")    
public String textUnescaped(Model model) {        
model.addAttribute("data", "<b>Hello Spring</b>");        
return "basic/text-unescaped";    
}
```

```html
<h1>text vs utext</h1>
<ul>  
	<li>th:text = <span th:text="${data}"></span></li>
	<li>th:utext = <span th:utext="${data}"></span></li>
</ul>
<h1><span th:inline="none">[[...]] vs [(...)]</span></h1>
<ul>
	<li><span th:inline="none">[[...]] = </span>[[${data}]]</li>
	<li><span th:inline="none">[(...)] = </span>[(${data})]</li>
</ul>
```

만약 "Hello Spring"이라는 문구를 <b></b> 태그를 사용해 Bold 효과를 넣어준다하였을 때, 모델에 data를 넘길 때 위처럼 줬을경우

Escape를 사용한다면 출력 결과로 원치않은 결과를 얻게 됩니다.

하지만 타임리프가 제공하는 Unescape를 사용하면 원하는 결과를 얻는 것을 볼 수 있습니다.

- th:utext
- [(...)]

![etc-image-3](https://blog.kakaocdn.net/dn/bLtOCS/btr7C1KrtA7/wlpjvzxahOwUSA5payvjxK/img.png)

---

##### SpringEL 변수 표현식

타임리프에서 변수를 사용할 때는 아래와 같은 변수 표현식을 사용합니다.

- ${...}

```java
@GetMapping("/variable")    
public String variable(Model model) {
	User userA = new User("userA", 10);
	User userB = new User("userB", 20);         
	List<User> list = new ArrayList<>();        
	list.add(userA);        
	list.add(userB);         
	Map<String, User> map = new HashMap<>();        
	map.put("userA", userA);        
	map.put("userB", userB);         
	model.addAttribute("user", userA);        
	model.addAttribute("users", list);        
	model.addAttribute("userMap", map);         
	return "basic/variable";    
}
```

```html
<body>
<h1>SpringEL 표현식</h1>
<ul>Object
	<li>${user.username} = <span th:text="${user.username}"></span></li>       
	<li>${user['username']} = <span th:text="${user['username']}"></span></li>   
	<li>${user.getUsername()} = <span th:text="${user.getUsername()}"></span></li>
</ul>
<ul>List    
	<li>${users[0].username} = <span th:text="${users[0].username}"></span>
	</li>    
	<li>${users[0]['username']} = <span th:text="${users[0]['username']}"></span>
	</li>
	<li>${users[0].getUsername()} = <span th:text="${users[0].getUsername()}"></span>
	</li>
</ul>
<ul>Map    
	<li>${userMap['userA'].username} = <span th:text="${userMap['userA'].username}"></span></li>    
	<li>${userMap['userA']['username']} = <span th:text=
	"${userMap['userA']'username']}"></span></li>
	<li>${userMap['userA'].getUsername()} = <span th:text="${userMap['userA']
	.getUsername()}"></span></li>
</ul> 
<h1>지역 변수 - (th:with)</h1>
<div th:with="first=${users[0]}">    <p>처음 사람의 이름은 <span th:text="${first.username}"></span></p></div></body>
```

![etc-image-4](https://blog.kakaocdn.net/dn/HERDg/btr7hwyo0Ga/EVGXH8YO1FDYLth2SQgMNK/img.png)

> **Object**

- user.username : user의 username을 프로퍼티 접근
- user['username'] : 위와 같음
- user.getUsername() : user의 getter 메서드 직접 호출

> **List**

- users[0].username : List에서 첫 번째 회원을 찾고 username 프로퍼티 접근
- users[0]['username'] : 위와 같음
- users[0].getUsername() : List에서 첫 번째 회원을 찾고 user의 getter 메서드 직접 호출

> **Map**

- userMap['userA'].username : Map에서 userA를 찾고, username 프로퍼티 접근
- userMap['userA']['username'] : 위와 같음
- userMap['userA'].getUsername() : Map에서 userA를 찾고 user의 getter 메서드 직접 호출

> **지역 변수 선언**

**th:with** 를 사용하면 지역 변수를 선언해서 사용할 수 있습니다.

단, 지역 변수는 선언한 태그 안에서만 사용할 수 있다는 점에 유의합니다.

---

##### 기본 객체와 편의 객체

> **기본 객체**

타임리프는 다음과 같은 기본 객체를 제공합니다.

- ${#request}
- ${#response} 
- ${#session} 
- ${#servletContext} 
- ${#locale}

하지만 #request 는 HttpServletRequest 객체가 그대로 제공되기 때문에 데이터를 조회하려면request.getParameter("data") 처럼 불편하게 접근해야 합니다.

그래서 이런 점을 해결하기 위해 타임리프는 편의 객체도 제공합니다.

> **편의 객체**

- param : HTTP 요청 파라미터 접근 - Ex: ${param.paramData}
- session : HTTP 세션 접근 - Ex: ${session.sessionData}
- @ : 스프링 빈 접근 - Ex: ${@helloBean.hello('Spring!')}

```java
@GetMapping("/basic-objects")
public String basicObjects(HttpSession session) {    
    session.setAttribute("sessionData", "Hello Session");
	return "basic/basic-objects";
} 
@Component("helloBean")
static class HelloBean {
	public String hello(String data) {        
		return "Hello " + data;	
	} 
}
```

```html
<body>
<h1>식 기본 객체 (Expression Basic Objects)</h1>
<ul>  
	<li>request = <span th:text="${#request}"></span></li>  
	<li>response = <span th:text="${#response}"></span></li>  
	<li>session = <span th:text="${#session}"></span></li>  
	<li>servletContext = <span th:text="${#servletContext}"></span></li>  
	<li>locale = <span th:text="${#locale}"></span></li>
</ul>
<h1>편의 객체</h1>
<ul>  
	<li>Request Parameter = <span th:text="${param.paramData}"></span></li>
	<li>session = <span th:text="${session.sessionData}"></span></li>
	<li>spring bean = <span th:text="${@helloBean.hello('Spring!')}"></span></li>
</ul>
</body>
```

![etc-image-5](https://blog.kakaocdn.net/dn/v1mq6/btr7xVKAwzP/bT1FO6FAj4wLSq5fDBpsn0/img.png)

![etc-image-6](https://blog.kakaocdn.net/dn/bUZIAn/btr7rufqT2b/GlvtGfPKPdsf6gXqjOkXj0/img.png)

---

##### 유틸리티 객체들

타임리프는 아래와 같이 문자, 숫자 날짜, URI등을 편리하게 다루는 다양한 유틸리티 객체들을 제공합니다.

- #message : 메시지, 국제화 처리
- #uris : URI 이스케이프 지원
- #dates : java.util.Date 서식 지원
- #calendars : java.util.Calendar 서식 지원
- #temporals : 자바8 날짜 서식 지원
- #numbers : 숫자 서식 지원
- #strings : 문자 관련 편의 기능
- #objects : 객체 관련 기능 제공
- #bools : boolean 관련 기능 제공
- #arrays : 배열 관련 기능 제공
- #lists , #sets , #maps : 컬렉션 관련 기능 제공
- #ids : 아이디 처리 관련 기능 제공

이 외에도 많은 유틸리티 객체들이 있으므로 필요시 아래 레퍼런스를 통해 찾아보면 됩니다.

> - [타임리프 유틸리티 객체](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#expression-utility)
> - [유틸리티 객체 예시](https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#appendix-b-expression)  

간단하게 Java8의 날짜 유틸리티 객체 사용법을 알아보면 다음과 같습니다.

```java
@GetMapping("/date")    
public String date(Model model) {
	model.addAttribute("localDateTime", LocalDateTime.now());
	return "basic/date";    
}
```

```html
<body>
<h1>LocalDateTime</h1>
<ul>  
	<li>default = <span th:text="${localDateTime}"></span></li>
	<li>yyyy-MM-dd HH:mm:ss = <span th:text="${#temporals.format(localDateTime,'yyyy-MM-dd HH:mm:ss')}"></span></li>
</ul>
<h1>LocalDateTime - Utils</h1>
<ul>
<li>${#temporals.day(localDateTime)} = <span th:text="${#temporals.day(localDateTime)}"></span></li>  
	<li>${#temporals.month(localDateTime)} = 
	<span th:text="${#temporals.month(localDateTime)}"></span></li>
	<li>${#temporals.monthName(localDateTime)} = 
	<span th:text="${#temporals.monthName(localDateTime)}"></span></li>
	<li>${#temporals.monthNameShort(localDateTime)} = 
	<span th:text="${#temporals.monthNameShort(localDateTime)}"></span></li>
	<li>${#temporals.year(localDateTime)} = 
	<span th:text="${#temporals.year(localDateTime)}"></span></li>  
	<li>${#temporals.dayOfWeek(localDateTime)} = 
	<span th:text="${#temporals.dayOfWeek(localDateTime)}"></span></li>
	<li>${#temporals.dayOfWeekName(localDateTime)} = 
	<span th:text="${#temporals.dayOfWeekName(localDateTime)}"></span></li>  
	<li>${#temporals.dayOfWeekNameShort(localDateTime)} = 
	<span th:text="${#temporals.dayOfWeekNameShort(localDateTime)}"></span></li>
	<li>${#temporals.hour(localDateTime)} = 
	<span th:text="${#temporals.hour(localDateTime)}"></span></li>
	<li>${#temporals.minute(localDateTime)} = 
	<span th:text="${#temporals.minute(localDateTime)}"></span></li>  
	<li>${#temporals.second(localDateTime)} = 
	<span th:text="${#temporals.second(localDateTime)}"></span></li>
	<li>${#temporals.nanosecond(localDateTime)} = 
<span th:text="${#temporals.nanosecond(localDateTime)}"></span></li>
</ul>
</body>
```

![etc-image-7](https://blog.kakaocdn.net/dn/ZIKOH/btr7hTm7PY3/Cl22wseAwFlG9ctrnv1t81/img.png)

---

##### URL 링크

타임리프에서 **URL을 생성할 때는 @{...} 문법**을 사용합니다.

> **- 단순 URL**  
> @{/hello} => /hello  
>   
> **- 쿼리 파라미터  
> **()에 있는 부분은 쿼리 파라미터로 처리됩니다.  
> @{/hello(param1=${param1}, param2=${param2})}  
> => /hello?param1=data1&param2=data2  
>   
> **- 경로 변수  
> **URL 경로 상에 변수가 있으면 ()은 경로 변수로 처리됩니다.  
> @{/hello/{param1}/{param2}(param1=${param1}, param2=${param2})}  
> => /hello/data1/data2  
>   
> **- 경로 변수 + 쿼리 파라미터  
> **경로 변수와 쿼리 파라미터를 함께 사용할 수 있습니다.  
> @{/hello/{param1}(param1=${param1}, param2=${param2})}  
> => /hello/data1?param2=data2

```java
@GetMapping("/link")    
public String link(Model model) {
	model.addAttribute("param1", "data1");
	model.addAttribute("param2", "data2");
	return "basic/link";    
}
```

```html
<body>
<h1>URL 링크</h1>
<ul>  
	<li><a th:href="@{/hello}">basic url</a></li>  
	<li><a th:href="@{/hello(param1=${param1}, param2=${param2})}">
	hello query param</a></li>
	<li><a th:href="@{/hello/{param1}/{param2}(param1=${param1},
	param2=${param2})}">path variable</a></li>
	<li><a th:href="@{/hello/{param1}(param1=${param1}, param2=${param2})}">
	path variable + query parameter</a></li>
</ul>
</body>
```

![etc-image-8](https://blog.kakaocdn.net/dn/bTt3BT/btr7pUrWS5n/YVhwQuoqugMdM8kSIKUnq0/img.png)

---

##### 리터럴

리터럴이란, **소스 코드상에 고정된 값**으로 예를 들어 아래 코드에서 "Hyun"은 문자 리터럴이고 20은 숫자 리터럴입니다.

```java
String name = "Hyun";
int age = 20;
```

타임리프에서는 문자 리터럴은 항상 ' ' 작은 따옴표로 감싸줘야합니다.

하지만 항상 작은 따옴표로 감싸는 것은 너무나도 귀찮은 일인데,

다행히도 타임리프는 공백 없이 쭉 이어진 문자라면 하나의 의미있는 토큰으로 인지해 작은 따옴표를 생략할 수 있습니다.
```html
 - 생략 가능한 예  
<span th:text="hello">
 - 생략 불가능한 예 (오류)  
 <span th:text="hello world">  
 ```
  중간에 띄어쓰기가 있기 때문에 작은 따옴표로 감싸줘야합니다.

```java
@GetMapping("/literal")    
public String literal(Model model) {
	model.addAttribute("data", "Spring!");
	return "basic/literal";    
}
```

```html
<body>
<h1>리터럴</h1>
<ul>  
	<li>'hello' + ' world!' = <span th:text="'hello' + ' world!'"></span></li>  
	<li>'hello world!' = <span th:text="'hello world!'"></span></li>  
	<li>'hello ' + ${data} = <span th:text="'hello ' + ${data}"></span></li>  
	<li>리터럴 대체 |hello ${data}| = <span th:text="|hello ${data}|"></span></li></ul></body>
```

![etc-image-9](https://blog.kakaocdn.net/dn/bb2Hzu/btr7hSIwu2g/7Csrz1eSHE06x8I6hatGhk/img.png)

> **리터럴 대체 문법**

<span th:text="|hello ${data}|"> 

문자 양쪽에 "|"를 감싸면, 위 처럼 +로 두 문자를 이어붙힐 필요없이 편하게 사용가능합니다.

---

##### 연산

타임리프에서 연산은 자바와 크게 다르진 않지만, HTML안에 사용하기 때문에 HTML 엔티티를 사용하는 부분에 주의해야합니다.

- 비교연산

|     |         |
| --- | ------- |
| >   | gt      |
| <   | lt      |
| >=  | ge      |
| <=  | le      |
| !   | not     |
| ==  | eq      |
| !=  | neq, ne |
|     |         |


- 조건식

자바의 조건식과 유사합니다.

- Elvis 연산자

조건식의 편의 버전으로<span th:text="${data}?: '데이터가없습니다.'">는 데이터 값을 받으면 그 데이터를 출력하고, 없다면 "데이터가없습니다"를 출력합니다.

- No-Operation

_ 인 경우 마치 타임리프가 실행되지 않는 것 처럼 동작해 HTML 의 내용을 그대로 활용할 수 있습니다.

```java
@GetMapping("/operation")
public String operation(Model model) {
	model.addAttribute("nullData", null); 
	model.addAttribute("data", "Spring!"); 
	return "basic/operation";
}
```

```html
<body>
<ul>  
	<li>산술 연산    
		<ul>      
			<li>10 + 2 = <span th:text="10 + 2"></span></li>     
			<li>10 % 2 == 0 = <span th:text="10 % 2 == 0"></span></li>
		</ul>  
	</li>  
	<li>비교 연산    
		<ul>     
			<li>1 > 10 = <span th:text="1 &gt; 10"></span></li>
			<li>1 gt 10 = <span th:text="1 gt 10"></span></li>
			<li>1 >= 10 = <span th:text="1 >= 10"></span></li>      
			<li>1 ge 10 = <span th:text="1 ge 10"></span></li>      
			<li>1 == 10 = <span th:text="1 == 10"></span></li>      
			<li>1 != 10 = <span th:text="1 != 10"></span></li>    
		</ul>  
	</li>  
	<li>조건식    
		<ul>      
			<li>(10 % 2 == 0)? '짝수':'홀수' = <span th:text="(10 % 2 == 0)? '짝수':'홀수'"></span></li>
		</ul>  
	</li>  
	<li>Elvis 연산자    
		<ul>      
			<li>${data}?: '데이터가 없습니다.' = <span th:text="${data}?: '데이터가없습니다.'"></span></li>      
			<li>${nullData}?: '데이터가 없습니다.' = <span th:text="${nullData}?: '데이터가 없습니다.'"></span></li>    
		</ul>  
	</li>  
	<li>No-Operation    
		<ul>      
			<li>${data}?: _ = <span th:text="${data}?: _">데이터가 없습니다.</span></li>      <li>${nullData}?: _ = <span th:text="${nullData}?: _">데이터가없습니다.</span></li> 
		</ul>  
	</li>
</ul>
</body>
```

![etc-image-10](https://blog.kakaocdn.net/dn/bGxOCx/btr7iKKgaK3/g8JkmeDXl4TmUY7LRmFM9k/img.png)

---

##### 반복문

타임리프에서 **반복은 th:each를 사용**합니다.

추가로 반복에서 사용할 수 있는 여러 상태 값을 지원하기도 합니다.

- 반복 : 
   ```html
  <tr th:each="user : ${users}">
  ```
  
반복시 오른쪽 컬렉션( ${users} )의 값을 하나씩 꺼내서 왼쪽 변수( user )에 담아서 태그를 반복 실행합니다. (향상된 for문과 동일)

th:each 는 List 뿐만 아니라 배열, java.util.Iterable , java.util.Enumeration 을 구현한 모든 객체를 반복에 사용할 수 있습니다.

Map 도 사용할 수 있는데 이 경우 변수에 담기는 값은 Map.Entry 입니다

- 반복 상태 유지 :  
  ```html
  <tr th:each="user, userStat : ${users}">
  ```

반복의 두번째 파라미터를 설정해서 반복의 상태를 확인 할 수 있습니다.

두번째 파라미터는 생략 가능한데, 생략하면 지정한 변수명( user ) + Stat 가 됩니다.

여기서는 user + Stat = userStat 이므로 생략 가능합니다.

```html
<tr th:each="user : ${users}"> 으로 수정해도 잘 동작
```

> **반복 상태 유지 기능**

- index : 0부터 시작하는 값
- count : 1부터 시작하는 값
- size : 전체 사이즈
- even , odd : 홀수, 짝수 여부( boolean )
- first , last :처음, 마지막 여부( boolean )
- current : 현재 객체

```java
@GetMapping("/each")public String each(Model model) {
	List<User> list = new ArrayList<>(); 
	list.add(new User("userA", 10)); 
	list.add(new User("userB", 20)); 
	list.add(new User("userC", 30));  
	model.addAttribute("users", list);  
	return "basic/each";
}
```

```html
<body>
<h1>기본 테이블</h1>
<table border="1">  
	<tr>    
		<th>username</th>
		<th>age</th>  
	</tr>  
	<tr th:each="user : ${users}">   
		<td th:text="${user.username}">username</td>    
		<td th:text="${user.age}">0</td>  
	</tr>
</table>
<h1>반복 상태 유지</h1>
<table border="1">  
	<tr>   
		<th>count</th>   
		<th>username</th>
		<th>age</th>    
		<th>etc</th>  
	</tr>  
	<tr th:each="user, userStat : ${users}">    
		<td th:text="${userStat.count}"></td>    
		<td th:text="${user.username}"></td>    
		<td th:text="${user.age}"></td>    
		<td>
			index = <span th:text="${userStat.index}"></span>      
			count = <span th:text="${userStat.count}"></span>      
			size = <span th:text="${userStat.size}"></span>      
			even? = <span th:text="${userStat.even}"></span>      
			odd? = <span th:text="${userStat.odd}"></span>      
			first? = <span th:text="${userStat.first}"></span>      
			last? = <span th:text="${userStat.last}"></span>      
			current = <span th:text="${userStat.current}"></span>    
		</td>  
	</tr>
</table>
</body>
```

![etc-image-11](https://blog.kakaocdn.net/dn/I75ZX/btr7pT7Gssr/lKHJZMbtO491vpI3FaGNSK/img.png)

---

##### 조건식

타임리프에서는 **조건식으로 if, unless(if의 반대), switch를 제공**합니다.

```java
@GetMapping("condition")    public String condition(Model model) {
	 List<User> list = new ArrayList<>();         
	 list.add(new User("userA", 10));         
	 list.add(new User("userB", 20));         
	 list.add(new User("userC", 30));          
	 model.addAttribute("users", list);         
	 return "basic/condition";    
 }
```

```html
<body>
<h1>if, unless</h1>
<table border="1">  
	<tr>    
		<th>count</th>
		<th>username</th>    
		<th>age</th>  
	</tr>  
	<tr th:each="user, userStat : ${users}">    
		<td th:text="${userStat.count}"></td>    
		<td th:text="${user.username}"></td>    
		<td>      
			<span th:text="${user.age}"></span>      
			<span th:text="'미성년자'" th:if="${user.age lt 20}"></span>      
			<span th:text="'미성년자'" th:unless="${user.age ge 20}"></span>    
		</td>  
	</tr>
</table>
<h1>switch</h1>
<table border="1">  
	<tr>    
		<th>count</th>
		<th>username</th>    
		<th>age</th>  
	</tr>  
	<tr th:each="user, userStat : ${users}">    
		<td th:text="${userStat.count}"></td>    
		<td th:text="${user.username}"></td>    
		<td th:switch="${user.age}">      
			<span th:case="10">10살</span>      
			<span th:case="20">20살</span>      
			<span th:case="*">기타</span>    
		</td>  
	</tr>
</table>
</body>
```

![etc-image-12](https://blog.kakaocdn.net/dn/bfJJrF/btr7hRiyCBP/98XLuxULdGazzCuwkWkMLk/img.png)

if, unless는 해당 조건이 맞지 않으면 태그 자체를 렌더링하지 않습니다.

만약 `<span th:text="'미성년자'" th:if="${user.age lt 20}">` 가 False일 경우 `<span> ~ </span>` 부분 자체가 렌더링 되지 않고 사라집니다.

Switch문에 사용된 "*"은 만족하는 조건이 없을 때 사용하는 Default 값입니다.

---

##### 블록

<th:block>은 HTML 태그가 아닌 **타임리프의 유일한 자체 태그**로

**each 반복문만으로 해결하기 어려울 때 블록단위로 반복문**을 돌릴 때 **사용**합니다.

```java
@GetMapping("/block")    
public String block(Model model) {         
    List<User> list = new ArrayList<>();         
    list.add(new User("userA", 10));         
    list.add(new User("userB", 20));         
    list.add(new User("userC", 30));          
    model.addAttribute("users", list);        
    return "basic/block";    
}
```

```html
<body>
<th:block th:each="user : ${users}">  
	<div>    
		사용자 이름1 <span th:text="${user.username}"></span>    
		사용자 나이1 <span th:text="${user.age}"></span>  
	</div>  
	<div>    
		요약 <span th:text="${user.username} + ' / ' + ${user.age}"></span>  
	</div>
</th:block>
</body>
```

HTML을 보면 두 `<div>`를 반복문을 돌리기 위해 사용하였습니다.

**`<th:block> ~ </th:block>` 안의 두 `<div>` 같이 반복문이 돈 것**을 볼 수 있씁니다.

![etc-image-13](https://blog.kakaocdn.net/dn/zVjvs/btr7xVKLUsc/ENmtM4oY5s2WuLsCNvVJS1/img.png)

---

##### 자바스크립트 인라인

타임리프는 자바스크립트에서 타임리프를 편리하게 사용할 수 있는 자바스크립트 인라인 기능을 제공합니다.

자바스크립트 인라인 기능은 다음과 같이 적용합니다.

|   |
|---|
|`<script th:inline="javascript">`|

```java
@GetMapping("/javascript")    
public String javascript(Model model) {        
	model.addAttribute("user", new User("userA", 10));        
	List<User> users = new ArrayList<>();        
	users.add(new User("UserA", 10));        
	users.add(new User("UserB", 20));        
	users.add(new User("UserC", 30));         
	model.addAttribute("users", users);        
	return "basic/javascript";    
}
```

```html
<body> 

<!-- 자바스크립트 인라인 사용 전 -->
<script>  
	var username = [[${user.username}]];  
	var age = [[${user.age}]];  
	//자바스크립트 내추럴 템플릿  
	var username2 = /*[[${user.username}]]*/ "test username";  
	//객체  
	var user = [[${user}]];</script> 
	
<!-- 자바스크립트 인라인 사용 후 -->
<script th:inline="javascript">  
	var username = [[${user.username}]];  
	var age = [[${user.age}]];  
	//자바스크립트 내추럴 템플릿  
	var username2 = /*[[${user.username}]]*/ "test username";  
	//객체  
	var user = [[${user}]];
</script> 

</body>
```

![etc-image-14](https://blog.kakaocdn.net/dn/beaWKK/btr7GATztrI/QmWRY7C4DPkQIyA9QIB4lK/img.png)

자바스크립트에서 변수값을 타임리프 변수로 대입할 때 자주 나는 문자열에 쌍따옴표로 감싸줘야합니다.

그래서 매번 따옴표로 감싸기에는 상당히 번거러움이 있습니다.

하지만 **자바스크립트** **인라인을 사용**하게 되면 알아서 따옴표를 추가해주기 때문에 **편리하게 사용**할 수 있습니다.  

|   |   |
|---|---|
|`var username = [[${user.username}]];`   |
|**인라인 사용 전**|
|**인라인 사용 후**|
|**var username = userA;**|
|**var username = "userA";**|

자바스크립트 인라인은 자바스크립트에서 문제가 될 수 있는 문자가 포함되어 있다면 **이스케이프 처리**도 해줄 뿐 아니라

자바스크립트 인라인을 적용하기 전에는 객체의 내용물을 그대로 출력하지만 인라인을 적용하고 난 후에는 **JSON형식으로 알아서 객체화**를 해줍니다.

> **JavaScript Natural Templates (자바스크립트 내추럴 템플릿)**

타임리프는 HTML 파일을 직접 열어도 동작하는 내추럴 템플릿을 제공합니다.

이때 자바스크립트 인라인을 사용하면 주석을 활용하여 이 기능을 사용할 수 있습니다.


|   |   
|---|
|`****var username2 = /*[[${user.username}]]*/ "test username";**** `|
|**인라인 사용 전**|
|**var username2 = /*userA*/ "test username";**|
|**인라인 사용 후**|
|**var username2 = "userA";**|

인라인 사용 전 결과를 보면 정말 순수하게 그대로 해석되었는데 내추럴 템플릿 기능이 동작하지 않고, 렌더링 내용이 주석처리 되어 버렸습니다.

반면에, 인라인 사용 후 결과를 보면 **주석 부분이 제거되고, 기대한 "userA"가 정확하게 적용된 것**을 볼 수 있습니다.

> **자바스크립트 인라인 each**

가끔씩 자바스크립트 인라인에서 반복문을 사용해야할 때가 있는데

이때는 다음과 같이 **반복문을 사용**할 수 있습니다.

```html
<script th:inline="javascript"> 
	[# th:each="user, stat : ${users}"] 
	var user[[${stat.count}]] = [[${user}]]; 
	[/]
</script>
```

![etc-image-15](https://blog.kakaocdn.net/dn/kBmbc/btr7g8Zk1nO/YyOuF9o87wty6QB7aO8lp1/img.png)
