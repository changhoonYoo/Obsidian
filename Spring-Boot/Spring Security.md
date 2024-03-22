### 시큐리티 기능 차단 설정

Spring Security 의존성을 추가했지만, 아직 인증단계를 개발하지 않은 경우 Application에서 exclude를 이용해 Security 기능을 꺼둘 수 있다.

`@SpringBootApplication(exclude = SecurityAutoConfiguration.class)`