### **R2DBC는 언제 사용할까?**

Reactive Programming을 하는 과정에서 Database 사용이 필요한 경우에 사용한다. 많은 예제로 Project Reactor 기반의 Spring Webflux를 사용하며 Database를 사용해야할 때 R2DBC를 많이 사용한다.

### **"엥? 그냥 Spring 진영에서 많이 사용하는 JPA를 사용할 수는 없나?"**

질문에 대한 대답은 **"사용할 수 있다. 하지만 사용하는 것이 좋진 않다."**

JPA는 기본적으로 비동기를 제공하지 않는다. 즉, Webflux 기반에서 JPA를 사용하면 Database 부분에서 [block](블로킹&논블로킹)되고, 그동안 thread가 기다리게 된다.

Webflux 같은 조금의 thread를 계속해서 사용하는 framework에서 이러한 작업은 비효율적이며 전체 시스템에 영향이 갈 수 있다. 결국 처음 말한 그대로 **Webflux 기반에서 JPA를 사용할 수는 있으나 사용하는 것이 좋지 않은 것**이다.

이러한 문제를 해결하기 위해 나온 것이 R2DBC이다. 이름이 좀 특이하다고 생각할 수 있다. 약간 스타워즈의 R2D2가 생각나기도 하고...근데 뭐 심오한 뜻이 있다기보다 **간단하게도 `Reactive Relational DataBase Connectivity`의 줄임말**이다.

- - -
### R2DBC를 사용하기 이전에 **3가지 주요사항**을 알아두자.

- R2DBC는 interface를 제공하는 `Specification` 일 뿐이다. 데이터베이스 벤더 측에서 해당 데이터베이스에 맞도록 구현해야만 한다.
- R2DBC는 [Reactive streams specification](https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.0/README.md#specification)을 기반으로 만들어졌다.
- R2DBC의 목적은 이름에서 알 수 있듯 기존 관계형 데이터베이스 driver specification에 대한 non-blocking alternative를 제공하는 것이다.

- - -
### 그럼, **R2DBC의 장점**은 무엇일까?

R2DBC는 Reactive programming 기반이다보니 Reactive programming이 가지는 장점을 그대로 갖는다.

즉, **Blocking programming일 때보다 높은 동시성(High Concurrency)이 요구되는 상황에서 더 좋은 성능**을 낼 수 있다.

성능에 대한 자세한 내용은 [Spring: Blocking vs non-blocking: R2DBC vs JDBC and WebFlux vs Web MVC 글](https://medium.com/oracledevs/spring-blocking-vs-non-blocking-r2dbc-vs-jdbc-and-webflux-vs-web-mvc-900d72ee19c1)을 참조하자.

해당 글에서 볼 수 있듯 **동시성(Concurrency)이 높아짐에 따라 MVC-JDBC를 사용하는 것보다 Latency는 더 줄었고, Throughput은 더 증가**한다.

- - -

### **"그럼 무조건적으로 R2DBC가 좋은 것이지 않아?"하는 질문에 대해서는 조심스럽게 접근**해야 한다. 그 이유는 여러가지가 있는데...

- 아직 기술측면에서 **숙련도가 높지 않다.** 즉, Blocking 방식에 비해 사용자도 많지 않고, 경험자도 많지 않다. 커뮤니티의 부재, 노하우 및 자료가 적음, 피드백을 받기 어려움 등 이슈가 있다.
- 구현체 측면에서도 아직 **제공되지 않는 데이터베이스 벤더가 있다. 또한, 공식적으로 벤더사에서 제공하지 않는 경우도 있다.** 이런 경우에 추후 관리 측면에서 문제가 발생할 수 있으므로 도입하는데 고려해봐야할 점이 많다.
- **Java future specification에도 영향**을 받을 수 있다. Project Loom이라고 하는 Blocking API에 반응성(Reactivity)을 가져오는 Java 프로젝트 등 Java 생태계에 Reactive를 다른 방향에서 제공할 수 있다.

또 위와 같이 이야기하면 도입이 무서워질 수 있는데, Spring 진영에서는 R2DBC를 위한 `Spring Data R2DBC` 를 지원하므로 위와 같은 걱정을 조금 줄이고 사용할 수 있다.

- 사실 그렇다고 spring data r2dbc가 완벽한 것은 아니다.
- 이번에 사용하면서 느낀건데 JPA 측 구현체. 즉, Hibernate/Spring data jpa는 정말 잘 만들어져있고, 제공하는 기능도 많다. 거기에 비하면 spring data r2dbc는 아직 멀게 느껴진다.
- - - 

