## 스프링 빈이란?

**스프링(Spring) 컨테이너가 관리하는 자바 객체를 빈(Bean)이라 한다.**

스프링의 특징에는 제어의 역전(IoC)이 있다.

제어의 역전(Inversion Of Control)이란, 
간단히 말해서 객체의 생성 및 제어권을 사용자가 아닌 스프링에게 맡기는 것이다. 지금까지는 사용자가 new연산을 통해 객체를 생성하고 메소드를 호출했다. IoC가 적용된 경우에는 이러한 객체의 생성과 사용자의 제어권을 스프링에게 넘긴다. 사용자는 직접 new를 이용해 생성한 객체를 사용하지 않고, 스프링에 의하여 관리당하는 자바 객체를 사용한다. 이 객체를 '빈(bean)'이라 한다.
	
## 빈 생명주기
Spring을 비롯해 많은 Spring Container는 자신이 관리하는 `Bean`의 Life-cycle을 관리해주고 특정 시점에 `Bean`에게 이를 알려줄 수 있는 메커니즘을 제공한다.

기본적으로 스프링 빈은 **객체 생성**->**의존관계 주입** 과 같은 라이프 사이클을 가진다.

스프링컨테이너 생성 -> 스프링 빈 생성 -> [[의존성 주입]] -> 초기화 콜백 -> 사용 -> 
소멸전 콜백 -> 스프링 종료

## @Bean 어노테이션의 어트리뷰트 사용


[@Bean](Annotation.md#^7f2fcd)`(initMethod = "init", destroyMethod = "close")`와 같이 설정 정보에 메서드를 지정가능하다.

```java
@Configuration
class MySpringConfiguration {

  @Bean(initMethod = "onInitialize", destroyMethod = "onDestroy")
  public MySpringBean mySpringBean() {
    return new MySpringBean();
  }
}
```

인터페이스 사용과 달리 메서드 이름을 자유롭게 지정할 수 있으며 스프링 코드에 의존하지도 않게 된다. 또한 설정 정보를 이용하므로 코드 변경이 불가한 외부 라이브러리에도 초기화 및 종료 메서드를 적용할 수 있다.

참고로 빈에 close() 혹은 shutdown() 메서드가 있다면 자동으로 소멸 전에 해당 메서드를 호출한다. 만약 이러한 추론 기능을 사용하기 싫다면 아래와 같이 세팅해주면된다.

```java
@Configuration
class MySpringConfiguration {

  @Bean(destroyMethod = "")
  public MySpringBean mySpringBean() {
    return new MySpringBean();
  }
}
```

## @PostConstruct, @PreDestroy 어노테이션 사용

pre-initialization 페이즈와 destroy 페이즈에 이 어노테이션들을 사용할 수 있다.

```java
@Component
class MySpringBean {

  @PostConstruct
  public void postConstruct() {
    //...
  }

  @PreDestroy
  public void preDestroy() {
    //...
  }
}
```

이 방법이 최신 스프링에서 가장 권장하는 방법이다. 스프링 종속적인 기술이 아닌 JSR-250 자바 표준에 속한 어노테이션이므로 스프링 의존적이지 않게 된다.  
외부 라이브러리에는 적용할 수 없으므로 만약 외부 라이브러리에 콜백을 사용하려면 @Bean 어트리뷰트 기능을 함께 사용해야 한다.