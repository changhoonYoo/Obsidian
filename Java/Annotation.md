## <span style="color:darkorange">@Bean</span>

`@Bean` 어노테이션은 스프링에서 빈(Bean)을 정의할 때 사용되는 어노테이션으로, 주로 `@Configuration` 어노테이션이 적용된 클래스에서 메서드에 사용됩니다. `@Bean` 어노테이션이 적용된 메서드는 해당 메서드가 반환하는 객체를 스프링 컨테이너가 빈으로 관리하도록 지정합니다.

```java
@Configuration 
public class AppConfig { 
	@Bean 
	public MyBean myBean() { 
		return new MyBean(); 
	} 
}
```
## <span style="color:darkorange">@Configuration</span>

`@Configuration` 어노테이션은 스프링에서 자바 기반의 설정 클래스를 정의할 때 사용됩니다. 이 어노테이션을 사용하면 해당 클래스가 스프링 애플리케이션 컨텍스트에서 빈 정의를 제공하며, 스프링 컨테이너에 의해 관리되게 됩니다.

기본적으로 `@Configuration` 어노테이션이 붙은 클래스 내부에서는 `@Bean` 어노테이션을 사용하여 빈을 정의할 수 있습니다. 이러한 빈은 스프링 애플리케이션 컨텍스트에서 사용 가능하며, 다른 빈들과 협력하여 의존성 주입 등을 통해 작동합니다.

간단한 `@Configuration` 클래스의 예시는 다음과 같습니다:
```java
import org.springframework.context.annotation.Bean; 
import org.springframework.context.annotation.Configuration; 

@Configuration 
public class AppConfig { 

	@Bean 
	public MyBean myBean() {
		return new MyBean();
	} 
}
```

위의 코드에서 `AppConfig` 클래스는 `@Configuration` 어노테이션이 적용되어 있습니다. 또한, `myBean` 메서드는 `@Bean` 어노테이션을 사용하여 `MyBean` 객체를 반환하고 있습니다. 따라서 이 클래스를 스프링 컨테이너에 등록하면 `MyBean`이라는 빈이 생성되고 관리됩니다.

`@Configuration` 어노테이션을 사용하는 것은 XML 기반의 설정 대신 자바 기반의 설정을 선호하는 스프링의 방식 중 하나입니다. 이를 통해 코드 기반의 설정을 통해 빈들을 명시적으로 정의하고, 의존성 주입 및 다양한 스프링 기능을 활용할 수 있습니다.
## <span style="color:darkorange">@ControllerAdvice</span> / <span style="color:darkorange">@RestControllerAdvice</span>

1. `@ControllerAdvice`:
	- `@ControllerAdvice` 어노테이션은 Spring MVC에서 예외 처리, 모델 속성 설정, 바인딩 및 전역 컨트롤러 설정을 담당하는 클래스에 사용됩니다.
	- 주로 예외 처리를 위해 활용되며, 여러 컨트롤러에서 발생한 예외를 한 곳에서 처리하고자 할 때 유용합니다.
	- `@ExceptionHandler` 메서드를 포함하는 클래스에 `@ControllerAdvice` 어노테이션을 추가하면 해당 메서드는 여러 컨트롤러에서 발생한 예외를 처리할 수 있습니다.
	- 또한, 전역적으로 사용되는 모델 속성을 초기화하거나, 바인딩 및 포맷 설정을 전역적으로 지정할 수 있습니다.
	```java
	import org.springframework.web.bind.annotation.ControllerAdvice; 
	import org.springframework.web.bind.annotation.ExceptionHandler;
	
	@ControllerAdvice 
	public class GlobalExceptionHandler { 
	
		@ExceptionHandler(Exception.class) 
		public String handleException(Exception ex) { 
			// 예외 처리 로직 
			return "error"; 
		} 
	}
	```
2. `@RestControllerAdvice`:
	- `@RestControllerAdvice`는 `@ControllerAdvice`와 유사하지만, 주로 RESTful 웹 서비스에서 사용됩니다.
	- `@ControllerAdvice`와 달리 `@ResponseBody`가 기본적으로 활성화되어 있어, 예외 처리 메서드의 반환값이 HTTP 응답의 본문으로 직접 전송됩니다.
	- 주로 REST API에서 예외 처리 및 응답의 표준화에 활용됩니다.
	```java
	import org.springframework.web.bind.annotation.ExceptionHandler; 
	import org.springframework.web.bind.annotation.RestControllerAdvice; 
	
	@RestControllerAdvice 
	public class GlobalRestControllerExceptionHandler { 
	
		@ExceptionHandler(Exception.class) 
		public ApiResponse handleException(Exception ex) { 
			// 예외 처리 로직 및 ApiResponse 객체 반환 
			return new ApiResponse("error", ex.getMessage()); 
		} 
	}
	```
	- 위의 예제에서 `ApiResponse`는 JSON 응답을 표현하는 사용자 정의 클래스입니다. 이렇게 구성된 `@RestControllerAdvice` 클래스는 예외가 발생하면 표준화된 JSON 응답을 반환하도록 처리할 수 있습니다.
- - -


## <span style="color:darkorange">@Modifying</span>

`@Modifying` 어노테이션은 Spring Data JPA에서 사용되며, 주로 `@Query` 어노테이션과 함께 사용됩니다. 이 어노테이션은 UPDATE나 DELETE와 같은 변경 쿼리를 실행할 때 사용되며, 해당 메서드가 데이터베이스의 내용을 변경할 것임을 나타냅니다.

간단히 말해, `@Modifying`은 데이터베이스의 상태를 변경하는 쿼리를 실행할 때 메서드에 지정됩니다.

아래는 `@Modifying` 어노테이션을 사용한 Spring Data JPA의 예제입니다:
```java
import org.springframework.data.jpa.repository.Modifying; 
import org.springframework.data.jpa.repository.Query; 
import org.springframework.data.repository.CrudRepository; 

public interface UserRepository extends CrudRepository<User, Long> { 

	@Modifying 
	@Query("UPDATE User u SET u.name = ?1 WHERE u.id = ?2") 
	void updateUserNameById(String newName, Long userId); 
}
```
위의 예제에서는 `@Modifying` 어노테이션이 `updateUserNameById` 메서드에 적용되었습니다. 이 메서드는 `User` 엔티티의 이름을 변경하는 JPQL(Java Persistence Query Language) 쿼리를 실행합니다. `@Modifying` 어노테이션을 사용하여 메서드가 데이터베이스의 내용을 변경함을 선언하였습니다.

중요한 점은 `@Modifying` 어노테이션이 있는 메서드는 `void`나 `int`와 같은 반환 타입을 가져야 합니다. 또한, 이 어노테이션을 사용하는 메서드는 주로 `@Query` 어노테이션과 함께 사용되며, 변경 쿼리를 명시적으로 지정하는 데 활용됩니다.
- - -
## <span style="color:darkorange">@NoArgsConstructor</span>

`@NoArgsConstructor`는 Lombok에서 제공하는 애노테이션 중 하나로, 해당 클래스에 매개변수가 없는 기본 생성자를 자동으로 생성해주는 기능을 제공합니다. 

이것은 주로 JPA 엔터티 클래스에서 많이 사용됩니다.
JPA에서 엔터티 클래스를 정의할 때, 매개변수가 없는 기본 생성자를 필요로 하는 경우가 많기 때문에 Lombok의 `@NoArgsConstructor`를 통해 편리하게 사용할 수 있습니다.

- - -
## <span style="color:darkorange">@RestResource</span>

`@RestResource(exported = ...)`는 Spring Data REST에서 사용되는 애노테이션 중 하나입니다. 이 애노테이션은 리소스를 노출할지 여부를 결정하는 데 사용됩니다.

기본적으로 Spring Data REST는 JPA 리포지토리의 메서드를 기반으로 RESTful 엔드포인트를 자동으로 생성합니다. 그러나 모든 리포지토리 메서드를 노출할 필요는 없으며, 특정 메서드만 노출하거나, 또는 특정 메서드를 제외하고 나머지를 노출하지 않을 수 있습니다.

(<span style="color:9999ff">exported</span> = true)
`@RestResource(exported = true)`는 해당 리포지토리 메서드를 노출하도록 지시하는 것입니다. 즉, 해당 메서드를 통해 접근 가능한 RESTful 엔드포인트가 생성됩니다. `exported` 속성의 기본값은 `true`이며, 이는 메서드가 노출된다는 것을 의미합니다.
```java
public interface UserRepository extends JpaRepository<User, Long> {

	@RestResource(exported = true) 
	List<User> findByLastName(String lastName); 
	// 이 메서드는 @RestResource가 없으므로 노출되지 않음 
	List<User> findByFirstName(String firstName); 
}
```

위 예제에서 `findByLastName` 메서드는 `@RestResource(exported = true)`로 인해 노출되지만, `findByFirstName` 메서드는 `@RestResource`가 없으므로 노출되지 않습니다.

이 경우, Spring Data REST는 자동으로 `/users/search/findByLastName`라는 엔드포인트를 생성합니다. 이 엔드포인트를 통해 `lastName` 매개변수를 사용하여 사용자를 찾을 수 있습니다.

반면에 `findByFirstName` 메서드에 `@RestResource`가 없는 경우, 해당 메서드로 생성되는 엔드포인트는 기본적으로 노출되지 않습니다.

즉, `@RestResource(exported = true)`를 사용하면 해당 메서드가 생성되는 RESTful 엔드포인트가 노출되고, `@RestResource`를 사용하지 않거나 `@RestResource(exported = false)`로 지정하면 해당 메서드로 생성되는 엔드포인트가 숨겨집니다.

- - -


## <span style="color:darkorange">@RequireArgsConstructor</span>

`@RequiredArgsConstructor`는 Lombok이 제공하는 어노테이션 중 하나로, 주로 생성자를 자동으로 생성해주는 데 사용됩니다. 이 어노테이션을 사용하면 클래스의 필드들을 파라미터로 받는 생성자가 자동으로 생성되어 코드를 간결하게 만들어줍니다.

1. **자동 생성자:**
	- `@RequiredArgsConstructor`를 클래스에 적용하면, 해당 클래스의 필드를 파라미터로 받는 생성자가 자동으로 생성됩니다. 
	- 생성자는 해당 필드들을 초기화하는 코드를 자동으로 생성해줍니다. 
	```java
	import lombok.RequiredArgsConstructor; 
	
	@RequiredArgsConstructor 
	public class Example {
		private final String field1; 
		private final int field2; 
	} 
	```
	위의 예제에서는 `field1`과 `field2`를 파라미터로 받는 생성자가 자동으로 생성됩니다.
	
2. **final 키워드 필요:**
	- `@RequiredArgsConstructor`를 사용할 때는 해당 필드들에 `final` 키워드가 붙어있어야 합니다.
	- 이는 Lombok이 생성자에서 초기화할 필드들을 결정하기 위함입니다.
	```java
	import lombok.RequiredArgsConstructor; 
	
	@RequiredArgsConstructor 
	public class Example { 
		private final String field1; 
		private int field2; // 컴파일 에러! 
		// @RequiredArgsConstructor를 사용하려면 final 필드여야 함 
	}
	```
- - -

## <span style="color:darkorange">@Log4j2</span>

`@Log4j2`는 Lombok에서 제공하는 어노테이션 중 하나로, Log4j 2를 사용하여 로깅 코드를 자동으로 생성해주는 데 사용됩니다. 이를 통해 코드를 간결하게 작성하고 로깅 구현을 쉽게 할 수 있습니다. 아래는 `@Log4j2` 어노테이션의 주요 속성과 기능에 대한 설명입니다.

1.  로거 필드 생성:
	-  `@Log4j2` 어노테이션을 클래스에 적용하면, 해당 클래스에 대한 `private static final Logger log = LogManager.getLogger(ClassName.class);`와 같은 로거 필드가 자동으로 생성됩니다.
	- 이 필드를 사용하여 로깅을 수행할 수 있습니다.
	```java
	import lombok.extern.log4j.Log4j2; 
	
	@Log4j2 
	public class Example {
		public void someMethod() { 
			log.info("This is an info message."); 
			log.error("This is an error message.", 
				new RuntimeException("Something went wrong.")); 
		} 
	}
	```
2. 로거 이름 지정:
	- `@Log4j2` 어노테이션을 사용할 때는 별도의 로거 이름을 지정하지 않아도 클래스 이름이 로거의 이름으로 사용됩니다. 따라서 로깅 구문에서 클래스 이름을 지정할 필요가 없어집니다.
3. **로깅 레벨에 따른 메서드 자동 생성:**

	- `@Log4j2` 어노테이션을 사용하면 다양한 로깅 레벨에 따라 다른 메서드가 자동으로 생성됩니다. 예를 들어, `log.info()`, `log.debug()`, `log.warn()` 등이 자동으로 사용 가능합니다.
	```java
	import lombok.extern.log4j.Log4j2;
	
	@Log4j2 
	public class Example { 
		public void someMethod() { 
			log.info("This is an info message."); 
			log.debug("This is a debug message."); 
			log.warn("This is a warning message."); 
		} 
	}
	```

로그 레벨은 로깅 메시지의 중요도에 따라 구분되며, 각 레벨은 다른 상황에서 사용됩니다. 일반적으로 다음과 같은 로그 레벨이 있습니다:

1. **DEBUG:**
    
    - `log.debug("This is a debug message.");`
    - 개발 중 디버깅을 위해 사용됩니다.
    - 상세한 디버그 정보를 기록하며, 보통 프로덕션 환경에서는 이 레벨의 로그를 최소화하는 것이 좋습니다.
2. **INFO:**
    
    - `log.info("This is an info message.");`
    - 어플리케이션의 주요 이벤트 및 상태 정보를 기록합니다.
    - 프로덕션 환경에서 주로 사용되며, 어플리케이션이 올바르게 동작하고 있는지 추적하는 데 사용됩니다.
3. **WARN:**
    
    - `log.warn("This is a warning message.");`
    - 경고 또는 잠재적인 문제를 나타내는 메시지를 기록합니다.
    - 어플리케이션이 예상치 못한 상황에 진입하거나 잠재적인 문제가 발생했을 때 사용됩니다.
4. **ERROR:**
    
    - `log.error("An error occurred.", e);`
    - 에러 상황을 나타내는 메시지를 기록합니다.
    - 예외 정보를 함께 기록하여 에러의 원인을 추적하는 데 사용됩니다.

로그 레벨은 로깅을 적절하게 관리하고 문제를 해결하는 데 도움이 됩니다. 개발 중에는 DEBUG 레벨을 사용하여 디버깅 정보를 확인하고, 프로덕션 환경에서는 INFO, WARN, ERROR 레벨을 조절하여 어플리케이션의 상태와 잠재적인 문제를 추적합니다. 예를 들어, INFO 레벨의 로그는 어플리케이션이 예상대로 동작하고 있는지 확인하는 데 사용되며, WARN 레벨은 잠재적인 문제를 식별하는 데 도움이 됩니다. ERROR 레벨은 심각한 에러 상황을 나타내며, 이를 통해 빠르게 문제를 진단하고 해결할 수 있습니다.
