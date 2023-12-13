
## <span style="color:orange">@NoArgsConstructor</span>

`@NoArgsConstructor`는 Lombok에서 제공하는 애노테이션 중 하나로, 해당 클래스에 매개변수가 없는 기본 생성자를 자동으로 생성해주는 기능을 제공합니다. 

이것은 주로 JPA 엔터티 클래스에서 많이 사용됩니다.
JPA에서 엔터티 클래스를 정의할 때, 매개변수가 없는 기본 생성자를 필요로 하는 경우가 많기 때문에 Lombok의 `@NoArgsConstructor`를 통해 편리하게 사용할 수 있습니다.

- - -
## <span style="color:orange">@RestResource</span>

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


## <span style="color:orange">@RequireArgsConstructor</span>

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
2. 