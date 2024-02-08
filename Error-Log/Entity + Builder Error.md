entity에 기본값 설정중에 에러가 발생

org.springframework.dao.DataIntegrityViolationException: not-null property references a null or transient value : kr.co.seoultel.momtMigrate.entity.mtdb.ArreoMms.msgGb

```java
@Entity
@Builder
public class ArreoMms {
 
	// 생략
	@Column(nullable = false)
    private String msgGb = "M";

```

기본값을 설정했는데도 인식을 못하고 에러 발생

이유는 @Builder로 생성할 때는 기본값을 인식 못한다.

```java
@Column(nullable = false) 
@Builder.Default // 디폴트 값을 설정하기 위한 어노테이션 
private String msgGb = "M"; // 디폴트 값으로 "M" 설정
```

@Builder.Default를 추가하여 디폴트값을 넣어 해결
