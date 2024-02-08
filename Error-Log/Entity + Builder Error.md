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

이유는 @Builder로 생성할 때는 