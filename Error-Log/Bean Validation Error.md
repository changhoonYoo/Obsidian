
- 라이브러리 추가
```xml
<dependency>  
   <groupId>javax.validation</groupId>  
   <artifactId>validation-api</artifactId>  
   <version>2.0.1.Final</version>  
</dependency>  
<dependency>  
   <groupId>org.hibernate.validator</groupId>  
   <artifactId>hibernate-validator</artifactId>  
   <version>6.0.11.Final</version>  
</dependency>
```


```java
public class SubmitRequestDto {  
  
    @NotBlank(message = "이름이 입력되지 않았습니다.")  
    private String name;
```

dto에서 @NotBlank 어노테이션 추가

