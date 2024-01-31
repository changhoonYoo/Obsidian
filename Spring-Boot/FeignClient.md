
## Feign Client 란?

Feign Client란 Netflix에서 개발한 Http Client 입니다.  
인터페이스를 작성하고 어노테이션을 통해 따로 구현체 없이 Http Client 를 구현 할 수 있습니다.  
여기서 Http Client 란 간단히 얘기해서 특정 프로그램에서 **Http 요청을 간편하게 만들어서 보낼 수 있도록 돕는 객체라고 생각하시면 됩니다.**  
원래는 Netflix 에서 자체적인 개발을 진행했지만 현재는 오픈 소스로 전환되어 SpringCloud 프레임 워크의 프로젝트 중 하나로 들어가 있습니다.

### 장점

1. SpringMvc에서 제공되는 어노테이션을 그대로 사용할 수 있습니다.
2. 기존에 사용되던 HttpClient 보다 더 간편하게 사용될 수 있으며 가독성 측면에서 뛰어납니다.
3. 통합 테스트가 비교적 간편합니다.
4. 사용자 의도대로 커스텀이 간편합니다.

> 의존성 추가

- 주의사항 : SpringBoot에 맞는 cloud 버전 내려받기!
- https://spring.io/projects/spring-cloud

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

> @EnableFeignClients 어노테이션 추가

```java
@SpringBootApplication
@EnableFeignClients // 여기
public class PracticeApplication {

	public static void main(String[] args) {
		 SpringApplication.run(PracticeApplication.class, args);

	}
}
```

Application에 붙이기 싫다면 configration 파일은 만들어서 설정해줘도 된다

```java
@Configuration
@EnableFeignClients(basePackages = "com.isntyet.java.practice")
	public class FeignClientConfig {
}
```

> Feign Client 인터페이스 구현

```java
  @FeignClient(name = "userClient", url = "https://randomuser.me")
  public interface UserClient {

      @GetMapping(value = "/api/")
      GetUsersResponse getUsers(@RequestParam("nat") String nation);
  }
```

```java
@FeignClient(value = "file-server", url = "${kr.co.file.url}", 
				 configuration = {FileCoKrHeaderConfig.class})  
public interface FileCoKrBinder {  
  
    @RequestMapping(value = "/json", method = RequestMethod.POST, 
    consumes = MediaType.MULTIPART_FORM_DATA_VALUE)  
    @Headers("Content-Type: multipart/form-data")  
    public void createJsonFile(@RequestPart String filename, @RequestPart MultipartFile file);  
  
  
    @RequestMapping(value = "/json/{data}", method = RequestMethod.POST, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)  
    @Headers("Content-Type: multipart/form-data")  
    public void createWavFile(@PathVariable String data, @RequestPart String filename, @RequestPart MultipartFile file);  
}
```

