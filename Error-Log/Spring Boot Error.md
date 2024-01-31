### @FeignClient 를 통한 통신 개발 중 에러

Spring boot 버전과 Spring cloud 버전의 불일치로 빌드에 계속 실패하는 상황

해결 :
- https://spring.io/projects/spring-cloud 중간 테이블에서 현재 스프링부트 버전에 맞는 cloud 버전을 찾아 pom.xml 에 디펜던시 추가

```xml
	<properties>  
	   <java.version>17</java.version>  
	   <spring-cloud.version>2022.0.3</spring-cloud.version>  
	</properties>

	...생략

	<dependencyManagement>  
	   <dependencies>  
	      <dependency>         
		      <groupId>org.springframework.cloud</groupId>  
	         <artifactId>spring-cloud-dependencies</artifactId>  
	         <version>${spring-cloud.version}</version>  
	         <type>pom</type>  
	         <scope>import</scope>  
	      </dependency>  
	   </dependencies>  
	</dependencyManagement>
	<dependencies>
		... 생략
		<dependency>  
		   <groupId>org.springframework.cloud</groupId>  
		   <artifactId>spring-cloud-starter-openfeign</artifactId>  
		</dependency>
	</dependencies>
```

```
feign.RetryableException: Connect timed out executing POST http://ip:8080/json
```

요청 결과 Connect timed out 발생 ->
