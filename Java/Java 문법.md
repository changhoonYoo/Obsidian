### default

-  example

```java
public interface ServiceOwnerRepository extends ...

default Page<Revision<Integer, ServiceOwner>> findRevisionsByCode(UUID code, Pageable pageable){  
    return findRevisions(a->{  
        return a.add(AuditEntity.property("code").eq(code));  
    }, pageable);  
}
```

`default` 키워드는 자바 8에서 추가된 기능 중 하나로, 인터페이스에 기본 구현을 제공할 수 있도록 합니다. 이렇게 하면 인터페이스를 구현하는 클래스에서 해당 메서드를 오버라이드하지 않아도 되며, 필요한 경우 오버라이드할 수 있습니다.

위의 코드에서 `default` 키워드는 `findRevisionsByCode` 메서드가 `RevisionRepository` 인터페이스에서 기본적으로 제공되는 메서드가 아닌, 해당 인터페이스를 상속받는 클래스에서 추가된 커스텀한 메서드임을 나타냅니다.

즉, `findRevisionsByCode` 메서드는 `RevisionRepository`에 기본적으로 정의되어 있지 않으며, 이 인터페이스를 구현하는 클래스에서 필요한 경우에만 해당 메서드를 구현할 수 있도록 하는데 사용되었습니다. 이렇게 하면 `RevisionRepository`의 다른 메서드들을 구현하는데 필요한 일반적인 기능들을 `default` 키워드를 사용하여 제공할 수 있습니다.

---

### instanceof


`instanceof`는 Java에서 사용되는 연산자로, 객체가 특정 클래스 또는 인터페이스의 인스턴스인지를 확인하는 데에 사용됩니다. 이 연산자는 주로 객체 지향 프로그래밍에서 다형성(polymorphism)을 활용할 때 객체의 타입을 확인하는데 유용합니다.

`instanceof`의 기본 문법은 다음과 같습니다:

```java
if (객체 instanceof 클래스 또는 인터페이스) {     
	// 객체가 해당 클래스 또는 인터페이스의 인스턴스일 때 실행되는 코드 }
}
```

여기서 `객체`는 확인하고자 하는 객체이고, `클래스 또는 인터페이스`는 해당 객체가 어떤 타입의 인스턴스인지를 나타냅니다.

예를 들어, 다음은 `instanceof`를 사용하여 객체가 어떤 클래스의 인스턴스인지를 확인하는 간단한 예제입니다:

```java
class Animal {     
	// 동물 클래스의 내용 
}  

class Dog extends Animal {     
	// 개 클래스의 내용 
}  

public class Main {     
	public static void main(String[] args) {   
	      
		Animal animal = new Dog();        
		  
		if (animal instanceof Dog) {             
			System.out.println("이 객체는 Dog 클래스의 인스턴스입니다.");         
		} else {             
			System.out.println("이 객체는 Dog 클래스의 인스턴스가 아닙니다.");
		}     
	} 
}
```

위의 예제에서 `animal`은 `Animal` 클래스의 변수지만, 실제로는 `Dog` 클래스의 인스턴스를 참조하고 있습니다. 따라서 `instanceof`를 사용하여 확인하면 결과는 "이 객체는 Dog 클래스의 인스턴스입니다."가 됩니다. 이러한 방식으로 `instanceof`는 객체의 실제 타입을 확인하는 데에 활용됩니다.

실전 사용 예제

- SSE 에러 핸들링 부분

```java
private void handleError(String number, Throwable error) {  
    SseEmitter emitter = emitterRepository.get(number);  
    if (emitter != null) {  
        try {  
            // 에러 발생 시 클라이언트에 에러 메시지 전송  
            emitter.send(SseEmitter.event().name("error").data(error.getMessage()));  
        } catch (IOException e) {  
            emitterRepository.deleteById(number015);  
            log.error("IOException : " + number015 + " Connection terminated");  
        } finally {  
            if (error instanceof IOException) {  
                emitter.completeWithError(error);  
                emitterRepository.deleteById(number015);  
            }  
        }  
    }  
}
```

---

### assert

Java에서 `assert`는 프로그램의 상태나 불변 조건을 확인하고 해당 조건이 참이 아닌 경우 프로그램을 종료하도록 하는 문장입니다. `assert`문은 디버깅 목적으로 사용되며, 코드가 어떤 상태에 있을 때 특정 조건이 참인지 확인하는 데 도움을 줍니다.

`assert`문은 보통 다음과 같은 형식을 갖습니다:
```java
assert expression;
```

여기서 `expression`은 참이나 거짓으로 평가될 수 있는 불리언 표현식입니다. 만약 `expression`이 거짓이라면 `AssertionError`가 발생하고 프로그램이 종료됩니다.

기본적으로 Java에서 `assert`문은 비활성화되어 있습니다. 따라서 `assert`문이 실행되려면 JVM을 `-ea` 또는 `enableassertions` 옵션으로 실행해야 합니다.

예를 들어:

```java
public class Example {     
	public static void main(String[] args) {         
		int x = 10;         
		assert x == 20 : "x should be 20"; 
		// 이 조건이 거짓이므로 AssertionError 발생         
		System.out.println("This line won't be reached.");     
	} 
}
```

이 코드는 실행 중에 `AssertionError`를 발생시키고 프로그램이 종료됩니다. `assert`문은 개발자가 가정한 조건이 프로그램이 실행되는 동안 계속 참인지 확인하기 위해 사용됩니다.