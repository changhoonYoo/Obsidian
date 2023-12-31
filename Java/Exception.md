## 자바 예외에 대해

![etc-image-0](https://blog.kakaocdn.net/dn/bBGQfL/btscmKZ6ZQF/Hn1tDn8ufkRUzaZ4eorKik/img.png)

| 예외               | 설명                                                                                         |
|--------------------|----------------------------------------------------------------------------------------------|
| Object             | 예외도 객체다. <br>모든 객체의 최상위 부모는 Object 이므로 예외의 최상위 부모도 Object 이다.     |
| Throwable          | 최상위 예외. <br>Throwable 하위에 Exception 과 Error 가 있다.                                    |
| Error              | 메모리 부족이나 심각한 시스템 오류와 같이 애플리케이션에서 복구 불가능한 시스템 예외 (언체크 예외) <br>이미 처리가 불가능한 오류 이므로 이 오류를 잡으려 해서는 안된다. |
| Exception          | 체크 예외 애플리케이션 로직에서 사용할 수 있는 실질적인 최상위 예외 <br>Exception 과 그 하위 예외는 모두 컴파일러가 체크하는 체크 예외 |
| RuntimeException    | 런타임 예외 컴파일러가 체크 하지 않는 언체크 예외 <br>RuntimeException 과 그 자식 예외는 모두 언체크 예외 <br>RuntimeException 과 그 하위 언체크 예외를 런타임 예외라고 부른다. |
<br>
예외를 잡아 처리할 때, **상위 예외를 catch 로 잡으면 그 하위 예외까지 함께** 잡게 됩니다.
위에서 Error 예외는 이미 처리가 불가능한 심각한 오류로 이 오류를 잡아선 안된다고 하였는데, Throwable을 예외를 잡았다면 그 하위인 Erorr도 같이 잡게 되므로 Throwable 예외도 잡아서는 안됩니다. 
따라서 애플리케이션 로직은 이런 이유로 **Exception 부터 필요한 예외로 생각하고 잡으면 됩니다.**

> **예외 기본 규칙** 

예외는 폭탄 돌리기 처럼 **잡아서 처리하거나, 처리할 수 없으면 밖으로 던져**야 합니다.
예외가 발생하는 로직을 호출한 곳에서 예외를 처리하면 애플리케이션 로직은 정상 동작하고, 예외를 처리하지 못하면 그 예외가 발생한 로직을 호출한 곳으로 계속 예외를 던지게 됩니다.
또한 예외를 잡거나 던질 때 지정한 예외 뿐 아니라 그 **예외를 상속하는 자식들도 함께 처리** 됩니다.

1. Exception 을 catch 로 잡으면 그 하위 예외들도 모두 잡을 수 있다.
2. Exception 을 throws 로 던지면 그 하위 예외들도 모두 던질 수 있다

만약 **예외를 처리하지 못하고 계속 던지게 된다면**
자바 main() 쓰레드의 경우 예외 로그를 출력하면서 **시스템이 종료**됩니다.
애플리케이션은 여러 사용자의 요청을 처리하기 때문에 하나의 예외로 시스템이 종료되게 된다면 모든 사용자가 그 서비스를 이용할 수 없는 참사가 발생하게 됩니다.
그래서 WAS가 해당 예외를 받아서 처리하도록 주로 사용자에게 **개발자가 지정한 오류 페이지를 보여주도록 하여 서버가 종료되지 않도록** 해야합니다.

---

## 체크 예외

체크 예외는 **RuntimeException**을 상속하지 않는 예외들로 **Exception** 과 그 하위 예외는 모두 컴파일러가 체크하는 체크 예외입니다.
체크 예외는 **잡아서 처리하거나, 또는 밖으로 던지도록 선언**해야 하는데, 그렇지 않으면 **컴파일 오류가 발생**합니다.
대표적으로 **IOException, SQLException** 등이 있습니다.

> **체크 예외의 장단점**

체크 예외는 예외를 잡아서 처리할 수 없을 때, 예외를 밖으로 던지는 throws 예외를 필수로 선언해야 하는데, 그렇지 않으면 컴파일 오류가 발생합니다.

여기서 장점과 단점이 있습니다.

- 장점 : 개발자가 실수로 예외를 누락하지 않도록 **컴파일러를 통해 문제를 잡아주는 훌륭한 안전 장치**이다.
- 단점 : 개발자가 모든 체크 예외를 반드시 잡거나 던지도록 처리해야 하기 때문에, **너무 번거로운** 일이 된다.

---

## 언체크 예외

**RuntimeException을 상속한 예외**들은 따로 **언체크 예외**라고 합니다.

언체크 예외는 말 그대로 **컴파일러가 예외를 체크하지 않는 예외** 입니다.

언체크 예외는 체크 예외와 다르게 예외를 **throws로 선언하지 않고 생략할 수 있으며,** **이 경우 자동으로 예외를 던집**니다.

대표적으로 **NullPointerException, IllegalArgumentException** 등이 있습니다.

> **언체크 예외의 장단점**

언체크 예외는 예외를 잡아서 처리할 수 없을 때, 예외를 밖으로 던지는 **throws 예외 를 생략**할 수 있는데,

이것 때문에 장점과 단점이 동시에 존재합니다.

- 장점 : 신경쓰고 싶지 않은 언체크 예외를 무시할 수 있다. 체크 예외의 경우 처리할 수 없는 예외를 밖으로 던지려면 항상 throws 예외 를 선언해야 하지만, 언체크 예외는 생략가능.
- 단점 : 언체크 예외는 컴파일러가 체크해주지 않기 떄문에, **개발자가 실수로 예외를 누락할 수 있다.**

---

## 체크 예외와 언체크 예외의 차이 및 활용

체크 예외와 언체크 예외의 차이는 크게 **예외를 처리할 수 없을 때 예외를 밖으로 던지는 부분**에 있습니다.

**throws를 필수로 선언해야 하는가, 생략할 수 있는가의 차이**

|             |                                                                               |
| --------------- | ----------------------------------------------------------------------------- |
| **체크 예외**   | **예외를 잡아서 처리하지 않으면 항상 throws 에 던지는 예외를 선언해야 한다.** |
| **언체크 예외** | **예외를 잡아서 처리하지 않아도 throws 를 생략할 수 있다.**                   |

> **체크 예외와 언체크 예외의 활용**

**기본적으로 언체크(런타임) 예외**를 사용하고, **체크 예외는 비즈니스 로직상 의도적으로 던지는 예외**에 사용합니다. 비즈니스 로직상 의도적으로 예외를 던져야 할 경우를 예를 들면 해당 예외를 잡아서 **반드시 처리해야 하는 문제일 경우**가 있습니다.

예를 들면, **계좌 이체 실패 예외, 로그인 ID, PW 불일치 예외** 등

이렇게 구분하여 예외를 활용하는 이유는 체크 예외는 예외 누락을 컴파일러가 체크해주기 때문에 무조건 처리해야하는 예외가 있다면 **체크 예외**를 활용해 개발자가 **실수로 예외를 놓치는 것을 막아**줄 수 있기 때문입니다.

그런데 이렇게만 보면 그냥 체크 예외 사용하면 안되나 ? 라는 생각이 들 수 있습니다.
왜 **체크 예외를 기본으로 사용하는 것이 문제**가 되는지 알아 봅시다.

> **체크 예외의 문제점**

**- 복구 불가능한 예외**

SQLException 을 예를 들면 데이터베이스에 무언가 문제가 있어서 발생하는 예외로,
SQL 문법에 문제가 있을 수도 있고, 데이터베이스 자체에 뭔가 문제가 발생했을 수도 있고, 데이터베이스 서버가 중간에 다운 되었을 수도 있습니다.

이런 문제들은 **시스템 레벨에서 발생한 문제이기 때문에 대부분 복구가 불가능**합니다.
특히 서비스나 컨트롤러는 이런 문제를 해결할 수는 없습니다.

따라서 이런 문제들은 일관성 있게 공통으로 처리해야 한다. 오류 로그를 남기고 개발자가 해당 오류를 빠르게 인지하는 것이 필요합니다.

서블릿 필터, 스프링 인터셉터, 스프링의 ControllerAdvice 를 사용하면 이런 부분을 깔끔하게 공통으로 해결할 수 있습니다.

**- 의존 관계에 대한 문제**

체크 예외의 또 다른 심각한 문제는 예외에 대한 의존 관계 문제입니다.

대부분의 예외는 시스템 레벨의 복구 불가능한 예외인데, 체크 예외이기 때문에 컨트롤러나 서비스 입장에서는 본인이 처리할 수 없어도 **어쩔 수 없이 throws 를 통해 던지는 예외를 선언**하는 문제가 있습니다.

---

## 체크 예외를 언체크 예외로 전환

시스템 레벨의 복구 불가능한 예외는 어차피 처리 못하는 문제이기 때문에  **언체크 예외로 변경**하여 throws 던지지 않고 생략하도록 할 수 있습니다.

> **런타임 예외를 상속한 RuntimeSQLException을 생성**

**Throwable cause** 라는 파라미터를 갖는 생성자를 생성하였는데,
이 객체가 상속 받은 RuntimeException 예외도 포함하여 전달해줍니다.
이렇게 예외를 전환할 때는 **꼭 기존 예외를 포함**하도록 해야 합니다.

```java
class RuntimeSQLException extends RuntimeException {    
	public RuntimeSQLException(Throwable cause) {    	
		super(cause);	
	}	
}
```

> **예외 전환**

예외를 전환 할 때 **발생한 예외를 잡고** **언체크(런타임) 예외로 바꾼 예외를 던지도록** 합니다.

```java
class Repository {    
	public void call() {        
		try {        	
			throw new SQLException("예외 발생");        
		} catch (SQLException e) {        	
			// 예외를 전환할 때는 꼭 기존 예외 포함! 
			// 그렇지 않으면 스택 트레이스를 확인할 때 심각한 문제가 발생한다.   
			throw new RuntimeSQLException(e);         
		}    
	}
}
```

---

## Spring에서의 예외

DB에서 오류가 발생하면 모든 Exception을 SQLException 하나에 모두 담아 버려 버립니다.
근데 SQLException은 체크 예외로 복구가 불가능한 예외 입니다.

따라서 예외를 처리할 수 없어도 **어쩔 수 없이 throws 를 통해 던지는 예외를 선언**해야 하게 되는데, 스프링은 **DB** **벤더마다 SQL 에러코드가 다르고, 관리하기 어려워** 자바에서 다루는 **SQLException(체크 예외)을 DataAccessException(언체크 예외)로 변환**하여 사용합니다.

![etc-image-1](https://blog.kakaocdn.net/dn/b54r3Y/btscPZ2xUGG/tSgPj0fC3Ek6GBhfEX46YK/img.png)

DataAccessException은 스프링 예외의 최상의 계층으로, RuntimeException을 상속받기 때문에 **스프링이 제공하는 데이터 접근 계층의 모든 예외는 런타임(언체크) 예외**입니다.

**- NonTransient 예외 :** 일시적인 예외로, 동일한 SQL을 수행할 경우 성공할 가능성이 있다.  
**- Transient 예외 :** 일시적이지 않은 예외, SQL 문법 오류나 제약조건 위배 등

DataAccessException는 특정 기술에 **종속적이지 않게 설계**되어 JDBC를 사용하든 JPA를 사용하든 스프링이 제공하는 예외를 사용할 수 있습니다. 
이렇게 DB별로 발생하는 예외를 스프링이 제공하는 DB 계층 예외로 변경해줄 수 있는 이유**는 DB에서 발생하는 예외의 ErrorCode를 읽어 스프링의 예외 변환기 SQLExceptionTranslator가 해결**해 주기 때문입니다.

> **Spring에서 체크 예외와 언체크 예외**

스프링은 **체크 예외는 커밋**하고, **언체크(런타임) 예외는 롤백** 시킵니다.

그런데 체크 예외도 롤백이 필요할 때가 있습니다.

그럴 때에는 @Transactional 옵션인 **rollbackFor 옵션을 사용해 체크 예외도 롤백하도록 설정**할 수 있습니다.

```java
@Transactional(rollbackFor = Exception.class)
```

출처: [https://hstory0208.tistory.com/entry/체크-예외Exception과-언체크-예외RuntimeException](https://hstory0208.tistory.com/entry/%EC%B2%B4%ED%81%AC-%EC%98%88%EC%99%B8Exception%EA%B3%BC-%EC%96%B8%EC%B2%B4%ED%81%AC-%EC%98%88%EC%99%B8RuntimeException) [< Hyun / Log >:티스토리]