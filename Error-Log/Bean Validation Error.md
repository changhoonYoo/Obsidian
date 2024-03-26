
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

- DTO에 어노테이션 추가
```java
public class SubmitRequestDto {  
  
    @NotBlank(message = "이름이 입력되지 않았습니다.")  
    private String name;
```

- Controller에 @Valid 추가
```java
@PostMapping("/submit")  
public ResponseEntity<?> submit(
@RequestPart(name = "file", required = false) MultipartFile file,
@RequestPart(name = "dto") @Valid SubmitRequestDto requestDto) {  
    try { //생략
```

- ExceptionHadler 추가
```java
@RestControllerAdvice  
public class GlobalExceptionHandler {  
  
    @ExceptionHandler(MethodArgumentNotValidException.class)  
    public ResponseEntity<String> handleValidationExceptions(MethodArgumentNotValidException ex) {  
        String errorMessage = Objects.requireNonNull(ex.getBindingResult().getFieldError()).getDefaultMessage();  
        return ResponseEntity.status(HttpStatus.BAD_REQUEST).body(errorMessage);  
    }  
}
```

Postman 테스트 결과 검증을 하지 않고 null값이 통과되었다.

해결법: 

- 라이브러리 변경(기존 디펜던시 주석처리)

```xml
<!--       <dependency>-->  
<!--         <groupId>javax.validation</groupId>-->  
<!--         <artifactId>validation-api</artifactId>-->  
<!--         <version>2.0.1.Final</version>-->  
<!--      </dependency>-->  
<!--      <dependency>-->  
<!--         <groupId>org.hibernate.validator</groupId>-->  
<!--         <artifactId>hibernate-validator</artifactId>-->  
<!--         <version>6.0.11.Final</version>-->  
<!--      </dependency>-->  
      <dependency>  
         <groupId>org.springframework.boot</groupId>  
         <artifactId>spring-boot-starter-validation</artifactId>  
      </dependency>
```

이유는 자세히 모르겠으나 라이브러리 변경 후 import를 다시했더니 정상적으로 처리됨
