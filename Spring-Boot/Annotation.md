## <span style="color:darkorange">@Aspect</span>

`@Aspect` 어노테이션은 스프링 프레임워크에서 AOP(Aspect-Oriented Programming)를 지원하기 위한 어노테이션입니다. AOP는 프로그램의 관심사를 모듈화하는 기법으로, 핵심 비즈니스 로직과 관련 없는 부가적인 기능(로깅, 트랜잭션 관리, 보안 등)을 분리하여 모듈화할 수 있습니다.

`@Aspect` 어노테이션을 사용하여 클래스를 Aspect로 지정할 수 있습니다. 
Aspect는 어떤 지점(Pointcut)에서 어떤 행위(Advice)를 취할지 정의합니다.

간단한 예제를 살펴보겠습니다. 
아래의 코드는 메서드 실행 시간을 측정하는 Aspect를 정의하는 예제입니다.

```java
import org.aspectj.lang.annotation.Aspect; 
import org.aspectj.lang.annotation.Before; 
import org.aspectj.lang.annotation.After; 
import org.aspectj.lang.annotation.Around; 
import org.aspectj.lang.ProceedingJoinPoint; 
import org.springframework.stereotype.Component; 

@Aspect 
@Component 
public class PerformanceAspect {      
	@Around("execution(* com.example.service.*.*(..))")     
	public Object measureExecutionTime(ProceedingJoinPoint joinPoint) 
	throws Throwable {         
		long startTime = System.nanoTime();         
		Object result = joinPoint.proceed();         
		long endTime = System.nanoTime();         
		long executionTime = endTime - startTime;
		System.out.println(joinPoint.getSignature() + " executed in " + 
		executionTime + "ns");
		return result;
	} 
}
```

위의 예제에서:

- `@Aspect` 어노테이션은 이 클래스가 Aspect임을 선언합니다.
- `measureExecutionTime` 메서드는 `@Around` 어노테이션을 가지고 있습니다. 이 어노테이션은 지정된 포인트컷(Pointcut)에서 실행되는 Advice를 정의합니다. 이 경우 `execution(* com.example.service.*.*(..))` 포인트컷은 `com.example.service` 패키지 내의 모든 메서드 호출 지점입니다.
- `Around` 어드바이스는 메서드 실행 전과 후에 실행됩니다. 이 경우 메서드 실행 시간을 측정하여 출력합니다.

이렇게 `@Aspect` 어노테이션을 사용하면 AOP를 쉽게 구현할 수 있습니다. Aspect를 사용하여 로깅, 트랜잭션 관리, 보안 등 다양한 부가적인 기능을 쉽게 추가할 수 있습니다.

- - - 
## <span style="color:darkorange">@Bean</span>

`@Bean` 어노테이션은 스프링에서 빈(Bean)을 정의할 때 사용되는 어노테이션으로, 주로 `@Configuration` 어노테이션이 적용된 클래스에서 메서드에 사용됩니다. `@Bean` 어노테이션이 적용된 메서드는 해당 메서드가 반환하는 객체를 스프링 컨테이너가 빈으로 관리하도록 지정합니다. ^7f2fcd

1. **빈 메서드 호출:**
	- `@Bean` 어노테이션이 적용된 메서드가 호출됩니다.
	- 해당 메서드는 빈으로 등록할 객체를 생성하고 반환합니다.
		```java
		@Configuration 
		public class AppConfig { 
		
			@Bean 
			public MyBean myBean() { 
				return new MyBean(); 
			} 
		}
		```
2. **객체 생성 및 초기화:**

	- 반환된 객체는 스프링 컨테이너에 의해 생성되고 초기화됩니다.
	- 초기화 메서드는 `initMethod` 속성이나 `@PostConstruct` 어노테이션으로 지정된 메서드가 호출됩니다.
		```java
		public class MyBean { 
			// 초기화 메서드 
			public void initialize() { 
				// 객체 초기화 로직 
			} 
			
			// 또는 @PostConstruct 어노테이션 사용 
			@PostConstruct 
			public void postConstruct() { 
				// 객체 초기화 로직 
			} 
		}
		```
3. **빈 등록 및 관리:**

	- 생성된 객체는 스프링 컨테이너에 의해 빈으로 등록되고 관리됩니다.
	- 빈 이름은 기본적으로 메서드 이름이나 `name` 속성에 지정된 이름으로 등록됩니다.
		```java
		MyBean myBean = applicationContext.getBean(MyBean.class);
		```
4. **빈 사용:**

	- 다른 빈이나 컴포넌트에서 `@Autowired` 어노테이션 등을 통해 해당 빈을 주입받아 사용할 수 있습니다.
		```java
		@Service 
		public class MyService { 
		
			@Autowired 
			private MyBean myBean;
			 // ... 
		}
		```
5. **빈 소멸:**
	- 빈이 더 이상 필요하지 않거나 컨테이너가 종료될 때, 빈 소멸 메서드가 호출됩니다.
	- 소멸 메서드는 `destroyMethod` 속성이나 `@PreDestroy` 어노테이션으로 지정된 메서드가 호출됩니다.
		```java
		public class MyBean { 
		
			// 소멸 메서드 
			public void cleanup() { 
				// 객체 소멸 로직 
			} 
			// 또는 @PreDestroy 어노테이션 사용 
			@PreDestroy 
			public void preDestroy() { 
				// 객체 소멸 로직 
			} 
		}
		```
		이러한 단계는 스프링의 IoC (Inversion of Control) 원칙을 따르며, 빈의 생명주기와 초기화, 소멸에 대한 제어를 개발자가 할 수 있도록 합니다. 이로 인해 객체 간의 결합도를 낮추고 유연하고 확장 가능한 애플리케이션을 개발할 수 있습니다.
- - - 
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


## <span style="color:darkorange">@DynamicInsert</span>

`@DynamicInsert` 어노테이션은 Hibernate를 사용하는 Java 엔터티 클래스에서 사용될 수 있는 어노테이션 중 하나입니다. 이 어노테이션은 엔터티가 INSERT 쿼리를 실행할 때, 모든 컬럼에 대한 값을 포함하지 않고, 값이 null 또는 기본값인 컬럼은 쿼리에서 제외하도록 하는 기능을 제공합니다.

일반적으로 Hibernate에서는 엔터티를 저장할 때 모든 컬럼에 대한 값을 지정해야 합니다. 그러나 `@DynamicInsert`를 사용하면, INSERT 쿼리를 실행할 때에만 해당되는 컬럼들만 지정하여 사용할 수 있습니다. 이를 통해 불필요한 데이터를 전송하는 것을 줄이고 성능을 향상시킬 수 있습니다.

예를 들어, 다음은 `@DynamicInsert`를 사용한 엔터티 클래스의 예입니다:

```java
import javax.persistence.Entity; 
import javax.persistence.GeneratedValue; 
import javax.persistence.GenerationType; 
import javax.persistence.Id; 
import org.hibernate.annotations.DynamicInsert;  

@Entity 
@DynamicInsert 
public class YourEntity {
	@Id     
	@GeneratedValue(strategy = GenerationType.IDENTITY)     
	private Long id;      
	private String name;    
	private String description;   
	   
	// getters, setters, 등 필요한 메서드들... 
}
```

위의 코드에서 `@DynamicInsert` 어노테이션이 `YourEntity` 클래스에 적용되어 있습니다. 이제 이 엔터티를 저장할 때 `description`이나 다른 기본값이나 null인 컬럼은 INSERT 쿼리에서 제외될 수 있습니다.
## <span style="color:darkorange">@FeignClient</span>

`@FeignClient`는 Spring Cloud에서 제공하는 어노테이션 중 하나로, 서버 간 통신을 쉽게 구현할 수 있도록 도와주는 기능을 제공합니다. 주로 마이크로서비스 아키텍처에서 서비스 간 통신을 위해 사용됩니다.

`@FeignClient`를 사용하면 HTTP 기반의 RESTful 서비스에 대한 클라이언트를 선언적으로 작성할 수 있습니다. 이 어노테이션은 Spring Cloud Netflix의 Feign이라는 라이브러리와 함께 사용되며, 내부적으로는 Ribbon과 함께 작동하여 [[로드 밸런싱]] 기능을 제공합니다. ^6abd56

간단한 사용 예시는 다음과 같습니다:
```java
import org.springframework.cloud.openfeign.FeignClient; 
import org.springframework.web.bind.annotation.GetMapping; 

@FeignClient(name = "example-service", url = "http://example-service/api") public interface ExampleFeignClient { 
	@GetMapping("/exampleEndpoint") 
	String getExampleData(); 
}
```
위의 코드에서:

- `@FeignClient` 어노테이션은 Feign 클라이언트를 정의하며, `name`은 클라이언트의 이름, `url`은 클라이언트가 통신할 서버의 기본 URL을 나타냅니다.
- `ExampleFeignClient` 인터페이스는 Feign 클라이언트의 메서드를 정의하며, 각 메서드는 서버의 엔드포인트와 매핑됩니다.

`@FeignClient` 어노테이션은 Spring Cloud의 다양한 기능과 함께 사용될 수 있으며, 서비스 디스커버리, 로드 밸런싱, 히스트릭스와 같은 기능들을 쉽게 적용할 수 있도록 지원합니다. 이를 통해 마이크로서비스 간의 통신을 간편하게 구현할 수 있습니다.

`@FeignClient` 어노테이션의 속성을 살펴보겠습니다:

```java
@FeignClient(value = "example-server", url = "${com.example.api.url}", 
			 decode404 = true)
```

1. **value (또는 name):**
    
    - `value` 또는 `name` 속성은 Feign 클라이언트의 이름을 지정합니다.
    - 이 이름은 다른 빈과 구분하기 위해 사용되며, 서비스 디스커버리에서도 사용될 수 있습니다.
	    `@FeignClient(value = "example-server")`
    
2. **url:**
    - `url` 속성은 클라이언트가 통신할 대상 서버의 기본 URL을 지정합니다.
    - 이 URL은 문자열 형태로 제공되며, 예를 들어 `${com.example.api.url}`처럼 프로퍼티를 사용하여 동적으로 설정할 수도 있습니다.
	    `@FeignClient(url = "${com.example.api.url}")`
    
3. **decode404:**
    
    - `decode404` 속성은 404 응답을 디코딩할지 여부를 결정합니다.
    - `true`로 설정하면 404 응답이 디코딩되어 반환값으로 처리됩니다.
    
    `@FeignClient(decode404 = true)`
    

따라서 주어진 어노테이션은 "example-server"라는 이름의 Feign 클라이언트를 정의하며, 클라이언트가 통신할 서버의 기본 URL은 `${com.example.api.url}`로 설정되어 동적으로 지정됩니다. 또한, 404 응답을 디코딩하도록 설정되어 있습니다.
- - - 

## <span style="color:darkorange">@JsonProperty</span> / <span style="color:darkorange">@JsonIgnore</span>

`@JsonProperty`는 Jackson 라이브러리에서 제공하는 어노테이션 중 하나입니다. Jackson은 JSON 데이터와 Java 객체 간의 변환을 쉽게 수행할 수 있도록 도와주는 라이브러리로, `@JsonProperty` 어노테이션은 이 변환 과정에서 사용됩니다.

주로 다음과 같은 경우에 사용됩니다:

1. **JSON 키 이름 지정:** Java 클래스의 필드 또는 메소드의 이름과 JSON 데이터의 키 이름이 다를 경우, `@JsonProperty`를 사용하여 매핑을 정의할 수 있습니다.
    
    ```java
	public class Person {    
	
		@JsonProperty("full_name")     
		private String fullName;      
		
		// 다른 필드들과 메소드들... 
	}     
	```


    위의 예제에서는 `fullName` 필드가 JSON 데이터에서 "full_name" 키와 매핑된다는 것을 나타냅니다.
    
2. **특정 필드를 무시:** 어떤 필드를 JSON으로 변환하거나 JSON에서 해당 필드를 읽지 않기를 원할 때, `@JsonProperty`를 사용하여 해당 필드를 무시하도록 지정할 수 있습니다.
    
    ```java
    public class Person {     
    
	    private String firstName;     
	    private String lastName;      
	    
	    @JsonIgnore     
	    public String getFullName() {         
		    return firstName + " " + lastName;     
		 }      
		 
		 // 다른 필드들과 메소드들... 
	 }
	```
    
    위의 예제에서 `getFullName` 메소드가 `@JsonIgnore`를 사용하여 JSON 변환에서 제외됩니다.
    

이러한 방식으로 `@JsonProperty` 어노테이션을 사용하여 JSON 데이터와 Java 객체 간의 매핑을 세밀하게 제어할 수 있습니다.

- - -
`@JsonProperty` 어노테이션은 Jackson 라이브러리에서 사용되며, 다양한 속성을 설정하여 JSON 데이터와 Java 객체 간의 매핑을 세밀하게 제어할 수 있습니다. 몇 가지 주요 속성은 다음과 같습니다:

1. **value**: 매핑할 JSON 속성의 이름을 지정합니다. 예를 들어, `@JsonProperty("name")`에서 "name"은 JSON 데이터의 키가 됩니다.
    
    ```java
    @JsonProperty("name") 
    private String fullName;
	```
    
2. **access**: 필드 또는 메소드에 대한 접근 권한을 설정합니다. 기본값은 `JsonAutoDetect.Visibility.DEFAULT`로, 이는 Jackson의 기본 가시성 규칙을 따릅니다.
    
    
   ```java
	@JsonProperty(value = "name", access = JsonProperty.Access.READ_ONLY)
	private String fullName;
	```
    
3. **defaultValue**: 값이 없을 경우 사용할 기본값을 설정합니다.
    
   ```java
	@JsonProperty(value = "age", defaultValue = "25") 
	private int age;
	```
   
    
4. **index**: 순서를 나타내는 정수 값을 설정합니다.
    
    
    ```java
    @JsonProperty(value = "address", index = 1) 
    private String homeAddress;
    ```
    
5. **required**: 속성이 반드시 존재해야 하는지 여부를 나타냅니다.
    
    
    ```java
    @JsonProperty(value = "email", required = true) 
    private String emailAddress;
    ```
    
6. **valueFormat**: 날짜 등 특정 형식의 값을 변환할 때 사용되는 포맷을 설정합니다.
    
    
    ```java
    @JsonProperty(value = "birthdate", valueFormat = "yyyy-MM-dd") 
    private Date birthDate;
    ```
    

이러한 속성들을 통해 `@JsonProperty` 어노테이션을 사용하여 JSON 데이터의 특정 키와 Java 객체의 필드 간의 매핑을 세밀하게 조정할 수 있습니다.
- - -

## <span style="color:darkorange">@JsonInclude</span>

REST API에서 DTO(Data Transfer Object)를 반환할 때 특정 필드의 값이 null인 경우 해당 필드를 제외하고 리턴하는 것은 가능합니다. 이를 위해 일반적으로 다음과 같은 방법을 사용할 수 있습니다:

1. **직렬화 옵션 설정**: 대부분의 프레임워크는 객체를 JSON 또는 XML로 직렬화할 때 특정 필드의 값을 무시하도록 설정할 수 있는 옵션을 제공합니다.
    
2. **커스텀 직렬화 로직**: 필요한 경우 특정 필드의 값을 확인하고 해당 필드를 제외하도록 커스텀 직렬화 로직을 작성할 수 있습니다.
    

예를 들어, Java와 Spring Framework를 사용한다고 가정하면, 다음과 같이 작업할 수 있습니다:

```java
import com.fasterxml.jackson.annotation.JsonInclude; 
import lombok.Data; 
@Data 
public class YourDTO { 
	private String field1; 
	
	@JsonInclude(JsonInclude.Include.NON_NULL) 
	// 이 옵션을 사용하여 null값인 경우 해당 필드를 무시합니다. 
	private String field2; 
	
	private String field3; 
	
	// 나머지 필드들 
}
```

위 코드에서 `@JsonInclude(JsonInclude.Include.NON_NULL)` 어노테이션은 Jackson 라이브러리를 사용하여 JSON 직렬화 시에 null 값을 무시하도록 지시합니다.

Spring Boot에서 해당 DTO를 컨트롤러에서 반환할 때 자동으로 JSON으로 직렬화되어 클라이언트에 반환될 것입니다. 필드 값이 null인 경우 해당 필드는 출력에서 생략됩니다.

이와 유사한 방법은 다른 언어와 프레임워크에서도 사용할 수 있으며, 해당 언어 및 프레임워크의 문서를 참조하여 구체적인 방법을 확인할 수 있습니다.

- 속성
	1. `JsonInclude.Include.ALWAYS`: 항상 포함됩니다.
	2. `JsonInclude.Include.NON_NULL`: `null` 값이 아닌 경우에만 포함됩니다.
	3. `JsonInclude.Include.NON_ABSENT`: `Optional`과 같은 경우에만 포함됩니다.
	4. `JsonInclude.Include.NON_EMPTY`: 값이 비어있지 않은 경우에만 포함됩니다.
	5. `JsonInclude.Include.CUSTOM`: 사용자 지정 필터를 사용하여 포함 여부를 결정할 수 있습니다.

---
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

## <span style="color:darkorange">@ModelAttribute</span> 

`@ModelAttribute`는 스프링 MVC에서 컨트롤러 메서드의 파라미터에 사용되는 어노테이션 중 하나입니다. 이 어노테이션은 요청 매핑 메서드에서 모델에 속성을 추가하거나 수정할 때 사용되며, 주로 폼 데이터를 전달받는 데에 활용됩니다.

아래는 `@ModelAttribute`의 주요 사용법과 설명입니다:

1. **메서드 파라미터에 사용:**
    
	```java
	@GetMapping("/example") 
	public String example(@ModelAttribute("myModel") MyModel myModel) {
		// 컨트롤러 로직     
		return "viewName"; 
	}
	```
    
    - `@ModelAttribute("myModel")`는 모델에 "myModel"이라는 이름으로 속성을 추가하고, 해당 모델을 메서드의 파라미터로 전달합니다.
    - 만약 모델 속성의 이름을 생략하면 클래스명에서 첫 글자를 소문자로 바꾼 이름이 기본적으로 사용됩니다.
2. **리턴 값으로 사용:**
    
    ```java
    @ModelAttribute("myModel") 
    public MyModel setupModel() {     
	    // 모델 초기화 로직     
	    return new MyModel(); 
    }
    ```
    
    - `@ModelAttribute` 어노테이션을 메서드 레벨에 사용하면 해당 메서드는 컨트롤러 메서드가 호출되기 전에 실행되어 모델 속성을 초기화합니다.
    - 이렇게 초기화된 모델 속성은 컨트롤러 메서드에서 사용할 수 있습니다.
3. **리턴 값 생략:**
    
    ```java
    @ModelAttribute 
    public void setupModel(Model model) {     
	    // 모델 초기화 로직     
	    model.addAttribute("myModel", new MyModel()); 
	 }
	```
    
    - 메서드의 리턴 타입이 `void`인 경우에는 메서드 이름이 모델 속성의 이름으로 사용됩니다.
4. **메서드 레벨에서 사용 시 주의사항:**
    
    - `@ModelAttribute`를 메서드 레벨에 사용할 때는 해당 메서드의 반환 값이 모델에 자동으로 추가되기 때문에, 동일한 이름의 모델 속성을 컨트롤러 메서드에서 사용하면 충돌이 발생할 수 있습니다.
    - 메서드 레벨에서 사용할 때는 주로 초기화 로직이나 여러 컨트롤러에서 공통으로 사용되는 모델 속성을 설정할 때 활용됩니다.

`@ModelAttribute`는 주로 폼 데이터를 전달받아 객체로 바인딩하거나, 모델에 미리 설정된 속성을 추가하는 등의 용도로 활용됩니다.

## <span style="color:darkorange">@NoArgsConstructor</span>

`@NoArgsConstructor`는 Lombok에서 제공하는 애노테이션 중 하나로, 해당 클래스에 매개변수가 없는 기본 생성자를 자동으로 생성해주는 기능을 제공합니다. 

이것은 주로 JPA 엔터티 클래스에서 많이 사용됩니다.
JPA에서 엔터티 클래스를 정의할 때, 매개변수가 없는 기본 생성자를 필요로 하는 경우가 많기 때문에 Lombok의 `@NoArgsConstructor`를 통해 편리하게 사용할 수 있습니다.

- - -

## <span style="color:darkorange">@RestControllerAdvice</span>

`@RestControllerAdvice`는 Spring MVC 애플리케이션에서 전역적으로 예외를 처리하고, 예외에 대한 응답을 일관되게 생성하는 데 사용되는 어노테이션입니다. 이 클래스는 `@ControllerAdvice`와 `@ResponseBody`를 함께 사용하는 것과 동일한 역할을 하며, JSON 또는 XML 형식의 응답을 생성합니다.

`@RestControllerAdvice` 클래스를 작성하면 여러 컨트롤러에서 발생하는 예외를 일관된 방식으로 처리할 수 있습니다. 예를 들어, 특정 예외에 대해 HTTP 응답 코드를 지정하거나, 에러 메시지를 포함하거나, 로깅을 수행하거나, 추가적인 정보를 제공할 수 있습니다.

일반적으로 사용되는 `@RestControllerAdvice`의 몇 가지 기능:

1. **전역 예외 처리:** 애플리케이션에서 발생하는 모든 예외를 처리할 수 있습니다.
2. **특정 예외 처리:** 특정 예외 유형에 대한 처리 로직을 정의할 수 있습니다.
3. **응답 커스터마이징:** 예외 발생 시 클라이언트에게 전송되는 응답의 형식을 변경할 수 있습니다.

간단한 예를 들어보겠습니다:
```java
@RestControllerAdvice 
public class GlobalExceptionHandler { 
	@ExceptionHandler(Exception.class) 
	public ResponseEntity<String> handleException(Exception e) { 
		// 예외 처리 로직 
		return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
			.body("서버 오류 발생: " + e.getMessage()); 
	} 
	@ExceptionHandler(MyCustomException.class) 
	public ResponseEntity<String> handleCustomException(MyCustomException e) {
		// 특정 예외에 대한 처리 로직 
		return ResponseEntity.status(HttpStatus.BAD_REQUEST)
			.body("클라이언트 요청 오류: " + e.getMessage()); 
	} 
	// 다른 예외 핸들러 메서드들... 
}
```

이렇게 작성된 `GlobalExceptionHandler`는 애플리케이션 어디에서든 발생하는 `Exception` 및 `MyCustomException`에 대한 예외 처리를 정의합니다. 발생한 예외에 따라 다양한 응답을 생성할 수 있습니다.
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


## <span style="color:darkorange">@RequestBody</span>

`@RequestBody`는 스프링 MVC에서 사용되는 어노테이션 중 하나로, HTTP 요청의 본문(body)을 메서드의 파라미터에 매핑하는 데에 사용됩니다. 주로 클라이언트가 JSON이나 XML 형식으로 데이터를 전송하고, 서버에서는 해당 데이터를 객체로 변환하는데 활용됩니다.

아래는 `@RequestBody`의 주요 사용법과 설명입니다:

1. **JSON 데이터 수신하기:**
    
    ```java
	@PostMapping("/example") 
	public ResponseEntity<String> handleRequest(@RequestBody MyRequestObject requestObject) {     
		 // requestObject를 사용하여 비즈니스 로직 수행     
		 return ResponseEntity.ok("Request handled successfully"); 
	}
	```
    
    - `@RequestBody` 어노테이션을 사용하여 클라이언트가 전송한 JSON 데이터를 `MyRequestObject` 클래스의 객체로 변환합니다.
2. **HTTP 요청의 Content-Type 지정:**
    
    - `@RequestBody`를 사용할 때 클라이언트가 전송한 데이터의 형식(Content-Type)을 지정할 수 있습니다. 예를 들어, JSON 형식의 데이터를 수신하는 경우:
    
    ```java
    @PostMapping(path = "/example", consumes = MediaType.APPLICATION_JSON_VALUE) 
    public ResponseEntity<String> handleJsonRequest(@RequestBody MyRequestObject requestObject) {
		// requestObject를 사용하여 비즈니스 로직 수행     
		return ResponseEntity.ok("JSON Request handled successfully"); 
	}
    ```
    
3. **다양한 데이터 형식 수신:**
    
    - `@RequestBody`는 기본적으로는 JSON 데이터를 처리하지만, 다양한 데이터 형식에 대한 처리를 지원합니다. 예를 들어, XML 형식의 데이터를 수신하려면 `consumes` 속성을 `MediaType.APPLICATION_XML_VALUE`로 지정할 수 있습니다.
    
    ```java
    @PostMapping(path = "/example", consumes = MediaType.APPLICATION_XML_VALUE) 
    public ResponseEntity<String> handleXmlRequest(@RequestBody MyRequestObject requestObject) {     
	    // requestObject를 사용하여 비즈니스 로직 수행     
	    return ResponseEntity.ok("XML Request handled successfully"); 
	 }
	```
    
4. **Map으로 데이터 수신:**
    
    - `@RequestBody`를 사용하여 데이터를 `Map`으로 수신할 수도 있습니다.
    
    ```java
    @PostMapping("/example") 
    public ResponseEntity<String> handleMapRequest(@RequestBody Map<String, Object> requestBody) {     
	    // requestBody를 사용하여 비즈니스 로직 수행     
	    return ResponseEntity.ok("Map Request handled successfully"); 
	 }
	```
    
    - 이 경우, 클라이언트가 전송한 JSON 데이터는 자동으로 `Map`으로 변환됩니다.
5. **주의사항:**
    
    - `@RequestBody`를 사용할 때는 HTTP 요청의 본문이 있어야 하므로, 주로 POST 또는 PUT 메서드에서 사용됩니다.
    - `@RequestBody`를 사용하는 메서드는 주로 JSON이나 XML 형식의 데이터를 수신하는 용도로 활용됩니다.

`@RequestBody`를 사용하면 클라이언트에서 전송한 데이터를 객체로 변환하여 컨트롤러 메서드에서 활용할 수 있으므로, RESTful API에서 클라이언트와 서버 간의 데이터 교환에 유용하게 활용됩니다.
## <span style="color:darkorange">@RequestParam</span>

`@RequestParam`은 스프링 프레임워크에서 HTTP 요청의 파라미터를 메서드의 파라미터로 전달받을 때 사용하는 애너테이션입니다. 이를 통해 쿼리스트링이나 폼 데이터 등을 컨트롤러 메서드로 쉽게 전달할 수 있습니다.

여러 사용 예시를 통해 자세히 살펴보겠습니다.

1. **기본 사용:**
    
    ```java
    @GetMapping("/example") 
    public String exampleMethod(@RequestParam String param1, 
									   @RequestParam int param2) {
		// 메서드 로직 
	}
	```
    
    위의 예제에서 `param1`과 `param2`는 각각 쿼리스트링이나 폼 데이터에서 전달된 `param1`과 `param2`의 값을 받습니다.
	<br>
2. **기본값 설정:**
 
    ```java
    @GetMapping("/example") 
    public String exampleMethod(@RequestParam(defaultValue = "default") 
									    String param1) {
		// 메서드 로직 
	}
    ```
    
    `defaultValue` 속성을 사용하여 파라미터가 전달되지 않은 경우 기본값을 설정할 수 있습니다.
	 <br>
3. **필수 파라미터로 지정:**
       
    ```java
    @GetMapping("/example") 
	public String exampleMethod(@RequestParam(required = true) String param) {
		// 메서드 로직 
	}
    ```
    
    `required` 속성을 사용하여 파라미터를 필수로 지정할 수 있습니다. 기본값은 `true`이며, 만약 필수 파라미터가 전달되지 않으면 400 Bad Request 오류가 발생합니다.
	    <br>
4. **여러 값 받기:**
    
    
    
    ```java
    @GetMapping("/example") 
    public String exampleMethod(@RequestParam List<String> values) {    
	     // 메서드 로직 
  }
	  ```
    
    여러 값을 받고자 할 때는 리스트로 받을 수 있습니다.
	    <br>
5. **파라미터 이름과 메서드 파라미터 명이 다를 경우:**
    
    
    ```java
    @GetMapping("/example") 
    public String exampleMethod(@RequestParam(name = "customName") String param) {     
	    // 메서드 로직 
	}
    ```
    
    파라미터의 이름이 메서드 파라미터 명과 다를 경우 `name` 속성을 사용하여 매핑할 수 있습니다.
    <br>
6. **모든 파라미터를 Map으로 받기:**
    
    ```java
	@GetMapping("/example") 
	public String exampleMethod(@RequestParam Map<String, String> params) {     
		// 메서드 로직 
	}
	```
    
    모든 파라미터를 Map으로 받을 수 있습니다.
    

`@RequestParam`은 주로 쿼리스트링이나 폼 데이터를 받을 때 사용되며, 간단하고 편리한 방법을 제공하여 컨트롤러 메서드에서 파라미터를 처리하는 데에 도움을 줍니다.
- - -

## <span style="color:darkorange">@RequestPart</span> 

`@RequestPart`는 스프링 MVC에서 사용되는 어노테이션 중 하나로, 멀티파트 요청에서 파일 업로드나 바이너리 데이터를 처리하는데 사용됩니다. 주로 HTML 폼에서 파일을 업로드할 때 또는 클라이언트에서 멀티파트 요청으로 데이터를 전송할 때 활용됩니다.

아래는 `@RequestPart`의 주요 사용법과 설명입니다:

1. **단일 파일 업로드:**
    
    ```java
    @PostMapping("/upload") 
    public ResponseEntity<String> handleFileUpload(@RequestPart("file") MultipartFile file) {     
	    // MultipartFile 객체를 사용하여 파일 업로드 처리     
	    return ResponseEntity.ok("File uploaded successfully"); 
	 }
    ```
    
    - `@RequestPart` 어노테이션을 사용하여 멀티파트 요청에서 "file" 파트를 가져와서 `MultipartFile` 객체로 처리합니다.
2. **멀티파트 요청에서 여러 파일 업로드:**
    
    ```java
    @PostMapping("/uploadMultiple") 
    public ResponseEntity<String> handleMultipleFileUpload(@RequestPart("files") List<MultipartFile> files) {     
	    // 여러 MultipartFile 객체를 사용하여 여러 파일 업로드 처리     
	    return ResponseEntity.ok("Multiple files uploaded successfully"); 
	 }
	```
    
    - `List<MultipartFile>`과 같은 컬렉션을 사용하여 여러 파일을 업로드할 수 있습니다.
3. **파일과 다른 파라미터 함께 전송:**
    
    ```java
    @PostMapping("/uploadWithParams") 
    public ResponseEntity<String> handleFileWithParams(         @RequestPart("file") MultipartFile file,         
    @RequestPart("metadata") MyMetadataObject metadata) {     
	    // MultipartFile과 다른 파라미터를 함께 처리     
	    return ResponseEntity.ok("File and metadata uploaded successfully"); 
	 }
	```
    
    - 여러 `@RequestPart` 어노테이션을 사용하여 여러 파트를 다룰 수 있습니다.
4. **다양한 파트 형식 처리:**
    
    ```java
    @PostMapping("/uploadWithCustomType") 
    public ResponseEntity<String> handleCustomType(@RequestPart("data") MyCustomType customType) {     
	    // 사용자 정의 객체를 사용하여 파트 처리     
	    return ResponseEntity.ok("Custom type uploaded successfully"); 
	 }
	```
    
    - 사용자가 정의한 객체(`MyCustomType`)를 사용하여 멀티파트 요청의 특정 파트를 처리할 수 있습니다.
5. **`consumes` 속성 사용:**
    
    ```java
    @PostMapping(path = "/uploadJson", consumes = MediaType.APPLICATION_JSON_VALUE) 
    public ResponseEntity<String> handleJsonData(@RequestPart("data") MyJsonData jsonData) {     
	    // JSON 데이터를 처리     
		 return ResponseEntity.ok("JSON data uploaded successfully"); 
	 }
	```
    
    - `consumes` 속성을 사용하여 특정 컨텐츠 타입을 지정할 수 있습니다.

`@RequestPart` 어노테이션은 멀티파트 요청에서 파트를 처리할 때 유용하게 사용됩니다. 파일 업로드와 함께 다양한 데이터 형식을 처리할 수 있어, 클라이언트에서 다양한 종류의 데이터를 서버로 전송할 때 활용됩니다.

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

## <span style="color:darkorange">@Scheduled</span> 

`@Scheduled`는 스프링 프레임워크에서 제공하는 어노테이션 중 하나로, 주기적으로 메서드를 실행하도록 스케줄링할 때 사용됩니다. 이 어노테이션은 백그라운드 작업, 일정 주기로 실행되어야 하는 작업 등을 구현할 때 편리하게 사용할 수 있습니다.

주요 특징과 사용법은 다음과 같습니다:

1. **메서드에 적용:**
    
    ```java
    import org.springframework.scheduling.annotation.Scheduled;  
    
    public class MyScheduledService {      
	    @Scheduled(fixedRate = 5000) // 5초마다 실행     
	    public void myScheduledMethod() {         
		    // 주기적으로 실행될 로직     
		 } 
	 }
	```
    
    - `@Scheduled` 어노테이션을 메서드에 적용하여 해당 메서드가 주기적으로 실행되도록 지정합니다.
2. **속성 설정:**
    
    - `fixedRate`, `fixedDelay`, 또는 `cron` 속성을 사용하여 주기를 설정할 수 있습니다.
    
    ```java
	@Scheduled(fixedRate = 5000) // 5초마다 실행 
	public void myScheduledMethod() {     
	 // 주기적으로 실행될 로직 
	}  
	
	@Scheduled(fixedDelay = 10000) // 이전 실행이 완료된 후 10초 후에 실행 
	public void anotherScheduledMethod() {     
	 // 다른 주기로 실행될 로직 
	}  
	
	@Scheduled(cron = "0 0 0 * * *") // 매일 자정에 실행 
	public void dailyScheduledMethod() {     
	 // 매일 자정에 실행될 로직 
	}
	```
    
3. **ThreadPool 설정:**
    
    - `ThreadPoolTaskScheduler`를 사용하여 스레드 풀을 설정할 수 있습니다.
    
    ```java
    import org.springframework.context.annotation.Bean; 
    import org.springframework.context.annotation.Configuration; 
    import org.springframework.scheduling.annotation.EnableScheduling; 
    import org.springframework.scheduling.concurrent.ThreadPoolTaskScheduler;  
    
    @Configuration 
    @EnableScheduling 
    public class SchedulingConfig {      
	    @Bean     
	    public ThreadPoolTaskScheduler taskScheduler() {         
		    ThreadPoolTaskScheduler taskScheduler = 
										    new ThreadPoolTaskScheduler();         
		    taskScheduler.setPoolSize(10);         
		    return taskScheduler;     
		 } 
	 }
	```
    
    - `@EnableScheduling`을 사용하여 스케줄링을 활성화하고, `ThreadPoolTaskScheduler` 빈을 설정하여 스레드 풀의 크기를 조정할 수 있습니다.
4. **Exception 처리:**
    
    - 스케줄링된 메서드에서 발생하는 예외를 처리하려면 해당 메서드 내에서 예외를 처리해야 합니다. 스케줄링 메서드에서 예외가 발생하면 해당 작업은 중단되고 다음 주기에서 다시 시도됩니다.
    
    ```java
    @Scheduled(fixedRate = 5000) 
    public void myScheduledMethod() {     
	    try {         
		    // 예외가 발생할 수 있는 로직     
		 } catch (Exception e) {         
			 // 예외 처리 로직     
		 } 
	 }
	```
    

`@Scheduled` 어노테이션을 사용하면 주기적으로 메서드를 실행할 수 있으며, 다양한 스케줄링 속성을 통해 실행 주기를 세밀하게 조정할 수 있습니다.

- SpringApplication 설정
```java
@SpringBootApplication  
@EnableScheduling
public class ArreoApiServer {  
  
   public static void main(String[] args) {  
      SpringApplication.run(ArreoApiServer.class, args);  
   }  
}
```

@Scheduled 규칙 
   - Method는 void 타입으로
   - Method는 매개변수 사용 불가 
---
## <span style="color:darkorange">@Transient</span>

`@Transient`는 JPA(Java Persistence API)에서 사용되는 어노테이션 중 하나입니다. 이 어노테이션은 엔티티 클래스의 특정 필드나 메소드를 영속성 컨텍스트에 저장하지 않도록 지정하는 데 사용됩니다. 즉, 데이터베이스에 해당 필드를 매핑하지 않도록 합니다.

영속성 컨텍스트는 JPA 엔터티의 상태를 추적하고 데이터베이스와의 상호작용을 관리하는데 사용됩니다. `@Transient` 어노테이션이 적용된 필드는 영속성 컨텍스트에 저장되지 않으며, 따라서 데이터베이스에도 매핑되지 않습니다.

예를 들어:

```java
import javax.persistence.Entity; 
import javax.persistence.Id; 
import javax.persistence.Transient;  
@Entity 
public class User {

	@Id     
	private Long id;      
	private String username;      
	private String password;      
	
	@Transient     
	private String temporaryData; // 이 필드는 데이터베이스에 매핑되지 않음      
	
	// 생성자, getter, setter, 기타 메소드 
}
```

위의 코드에서 `temporaryData` 필드에 `@Transient` 어노테이션이 적용되어 있습니다. 이 필드는 데이터베이스에 매핑되지 않으며, 따라서 해당 필드의 값은 영속성 컨텍스트에 저장되지 않습니다.

`@Transient`를 사용하는 이유로는 데이터베이스에 저장할 필요가 없는 특정 정보나 계산된 값을 표현할 때, 또는 특정 필드를 무시하고 싶을 때 사용될 수 있습니다.
## <span style="color:darkorange">@Validated</span>

`@Validated` 어노테이션은 Spring MVC에서 사용되는 것으로, 메소드 파라미터나 리턴 타입에 대한 검증을 수행할 때 사용됩니다. 
주로 `@PathVariable`, `@RequestParam`, `@RequestHeader` 등의 어노테이션과 함께 사용됩니다.

`@Validated` 어노테이션을 사용하면, 해당 메소드의 파라미터나 리턴 타입에 대한 검증이 수행됩니다. 이때 검증은 `javax.validation` 패키지에 있는 어노테이션들을 사용하여 정의됩니다. 이 어노테이션들은 예를 들어 `@NotNull`, `@Size`, `@Pattern` 등이 있습니다.

예를 들어, 다음과 같이 `@Validated` 어노테이션을 사용하여 메소드 파라미터에 대한 검증을 수행할 수 있습니다.

```java
import org.springframework.validation.annotation.Validated; 
import org.springframework.web.bind.annotation.PathVariable; 
import org.springframework.web.bind.annotation.RestController; 

import javax.validation.constraints.NotBlank;

@RestController 
@Validated 
public class MyController { 

	@GetMapping("/010-number/{headNumber}-{tailNumber}") 
	public ResponseEntity<?> recommend( 
	@PathVariable("headNumber") @NotBlank String headNumber, 
	@PathVariable("tailNumber") @NotBlank String tailNumber) { 
	
	// 경로 변수가 숫자인 경우에만 실행되는 로직 
	int head = Integer.parseInt(headNumber); 
	int tail = Integer.parseInt(tailNumber); 
	return ResponseEntity.ok("Head Number: " + head + ", Tail Number: " + tail); 
	} 
}
```

이 코드에서 `@NotBlank` 어노테이션은 파라미터 값이 비어있지 않은지 검증합니다. 만약 비어있다면, `MethodArgumentNotValidException` 예외가 발생하고 클라이언트에게 `400 Bad Request` 응답이 반환됩니다.

`@Validated` 어노테이션은 `@Controller`, `@RestController`, `@Service`, `@Component` 등의 빈에 사용할 수 있습니다. 이 어노테이션은 메소드 파라미터나 리턴 타입에 대한 검증을 수행하기 때문에, 메소드에 `@Validated` 어노테이션을 사용하면 해당 메소드의 파라미터나 리턴 타입에 대한 검증이 수행됩니다.

---

## <span style="color:darkorange">@Value</span>
`@Value`는 스프링 프레임워크에서 제공하는 어노테이션 중 하나로, 주로 프로퍼티 값을 주입받아 필드에 할당하고자 할 때 사용됩니다. 이 어노테이션은 `@ConfigurationProperties`와 유사하지만, 좀 더 간편한 형태로 사용할 수 있습니다.

아래는 `@Value` 어노테이션의 주요 사용법에 대한 예제입니다:

1. **기본 사용:**
    
    - 프로퍼티 값을 필드에 주입하는 가장 기본적인 형태입니다.
    
		```java
		import org.springframework.beans.factory.annotation.Value; 
		import org.springframework.stereotype.Component;  
		
		@Component 
		public class MyComponent {     
		 
			@Value("${my.property}")     
			private String myProperty;    
			  
			// getter, setter 등 생략 
		}
		```
    
    - `${my.property}`는 프로퍼티 파일에서 `my.property` 키에 해당하는 값을 가져와서 `myProperty` 필드에 주입합니다.
    
1. **기본값 설정:**
    
    - 만약 프로퍼티 값이 없는 경우 기본값을 설정할 수 있습니다.
    
	    ```java
	    @Value("${my.property:default-value}") 
	    private String myProperty;
		```
    
    - `my.property`이 존재하지 않으면 `default-value`가 `myProperty` 필드에 할당됩니다.
3. **SpEL(스프링 표현 언어) 사용:**
    
    - SpEL을 사용하여 복잡한 표현식을 이용할 수 있습니다.
    
	    ```java
	    @Value("#{systemProperties['java.home']}") 
	    private String javaHome;
	    ```
    
    - 이 예제에서는 자바 홈 디렉토리를 시스템 프로퍼티에서 가져와서 `javaHome` 필드에 할당합니다.
4. **리스트나 배열로 값 주입:**
    
    - 여러 값을 한 번에 주입받을 수도 있습니다.
    
	    ```java
		@Value("${my.list.values}") 
		private List<String> myListValues; 
		
		@Value("${my.array.values}") 
		private String[] myArrayValues;
	    ```
    
    - 프로퍼티에서는 쉼표(,)로 구분된 값들을 설정해야 합니다.

이렇게 `@Value` 어노테이션을 사용하면 스프링 애플리케이션 컨텍스트에서 프로퍼티 값을 주입받을 수 있습니다. 주입받은 값을 필드에 할당하여 해당 빈을 사용하는데 활용할 수 있습니다.
- - -
`@Value` 어노테이션을 사용할 때 주입받는 프로퍼티는 주로 `application.properties` 또는 `application.yml` 등의 프로퍼티 파일에서 설정됩니다. 아래는 `application.properties` 파일의 예제와 함께 `@Value`를 사용한 코드입니다.

1. **`application.properties` 파일:**
```properties
my.property=Hello, World! 
my.list.values=value1,value2,value3
my.array.values=valueA,valueB,valueC
```
1. **`MyComponent` 클래스:**
```java
import org.springframework.beans.factory.annotation.Value; 
import org.springframework.stereotype.Component;  
import java.util.List;  

@Component 
public class MyComponent {      

	@Value("${my.property}")     
	private String myProperty;      
	
	@Value("${my.list.values}")     
	private List<String> myListValues;      
	
	@Value("${my.array.values}")     
	private String[] myArrayValues;      
	
	// getter, setter 등 생략      
	public void printValues() {         
		System.out.println("myProperty: " + myProperty);
		System.out.println("myListValues: " + myListValues);
		System.out.println("myArrayValues: " + 
							Arrays.toString(myArrayValues));
	} 
}
```

3. **활용하는 코드:**
    
```java
import org.springframework.beans.factory.annotation.Autowired; 
import org.springframework.boot.CommandLineRunner; 
import org.springframework.stereotype.Component; 

@Component 
public class AppRunner implements CommandLineRunner {      
	@Autowired     
	private MyComponent myComponent;      
	
	@Override     
	public void run(String... args) {         
		myComponent.printValues();     
	} 
}
```
    

위 코드에서 `MyComponent`는 `application.properties` 파일에서 정의한 값을 주입받아서 출력하는 간단한 예제입니다. `AppRunner`는 스프링 부트 애플리케이션이 실행될 때 `MyComponent`의 `printValues` 메서드를 호출하여 값을 출력합니다. 이때 `@Value` 어노테이션을 사용하여 프로퍼티 값을 주입받습니다.

- - -
## <span style="color:darkorange">@Qualifier</span>
`@Qualifier`는 Spring 프레임워크에서 사용되는 애노테이션으로, 주입할 빈을 명시적으로 지정할 때 사용됩니다.

Spring에서 같은 타입의 빈이 여러 개일 때, 어떤 빈을 주입해야 하는지를 구분하기 위해 `@Qualifier`를 사용합니다. 주로 의존성 주입(Dependency Injection) 시에 같은 타입의 빈이 여러 개일 때 발생하는 모호성을 해결하기 위해 활용됩니다.

예를 들어, 다음과 같이 사용될 수 있습니다:

javaCopy code
```java
@Component 
public class MyService {          
	private final MyRepository myRepository;      
	@Autowired    
	public MyService(@Qualifier("myRepositoryImpl") MyRepository myRepository) {
	     this.myRepository = myRepository;     
	} 
}
```

위의 예제에서 `MyService` 클래스는 `MyRepository` 타입의 빈을 주입받습니다. 하지만 `MyRepository` 타입의 빈이 여러 개인 경우, `@Qualifier`를 사용하여 명시적으로 어떤 빈을 주입받을지 지정할 수 있습니다. 위의 코드에서는 `"myRepositoryImpl"`이라는 이름을 가진 빈을 주입받도록 지정하고 있습니다.

---

