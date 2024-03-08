![[Pasted image 20240308103107.png]]

"웹훅(Webhook)"은 웹 애플리케이션에서 다른 애플리케이션으로 데이터를 전송하기 위한 방법입니다. 보통 이벤트 발생 시 특정 URL로 HTTP POST 요청을 보내는 것으로 구현됩니다.

웹훅은 일반적으로 이벤트 기반 시스템에서 사용되며, 다음과 같은 상황에서 유용합니다:

1. **실시간 알림**: 어떤 이벤트가 발생했을 때 다른 시스템에 즉각적으로 알림을 보내야 할 때 사용됩니다. 예를 들어, 새로운 이메일이 도착했을 때 이메일 클라이언트가 서버에게 새 이메일을 받았음을 알려주는 경우가 있습니다.
    
2. **자동화**: 다른 시스템에서 특정 이벤트가 발생했을 때 자동으로 작업을 실행하도록 할 때 사용됩니다. 예를 들어, 버전 관리 시스템에서 새로운 코드가 푸시되었을 때 CI/CD 도구에게 새로운 빌드를 시작하도록 알릴 수 있습니다.
    

웹훅을 구현하려면 다음이 필요합니다:

1. **이벤트 소스**: 웹훅을 발생시키는 시스템 또는 서비스가 있어야 합니다. 이벤트가 발생할 때마다 웹훅을 보낼 준비가 되어 있어야 합니다.
    
2. **웹훅 엔드포인트**: 웹훅을 수신하는 URL이 필요합니다. 이 URL은 이벤트를 받아들이고 처리하는 서버의 엔드포인트입니다.
    
3. **웹훅 페이로드**: 이벤트와 관련된 데이터를 포함하는 HTTP POST 요청의 페이로드입니다. 이 페이로드는 이벤트 소스에서 웹훅 엔드포인트로 전송됩니다.
    

웹훅은 실시간 통신을 통해 데이터를 전송하므로 비동기적으로 동작합니다. 이는 이벤트 소스가 웹훅을 트리거하고 나서 즉시 응답을 기다리지 않고 다른 작업을 계속할 수 있다는 것을 의미합니다. 이러한 특성으로 인해 웹훅은 다른 시스템 간의 효율적인 통신에 적합합니다.

```java
import org.springframework.http.HttpEntity; 
import org.springframework.http.HttpHeaders; 
import org.springframework.http.HttpMethod; 
import org.springframework.http.MediaType; 
import org.springframework.web.client.RestTemplate; 
import java.util.List; 
import java.util.concurrent.Executors; 
import java.util.concurrent.ScheduledExecutorService; 
import java.util.concurrent.TimeUnit; 

public class WebhookNotifier { 

	private final RestTemplate restTemplate; 
	
	public WebhookNotifier() { 
		this.restTemplate = new RestTemplate(); 
	} 
	
	// 데이터베이스 변경을 감지하고 웹훅을 호출하는 메서드 
	public void pollDatabaseAndNotify(JdbcTemplate jdbcTemplate) { 
		// 주기적으로 데이터베이스를 폴링 변경 사항을 감지, 변경이 있을 때 웹훅을 호출 
		ScheduledExecutorService scheduler= Executors.newScheduledThreadPool(1); 
		scheduler.scheduleAtFixedRate(() -> { 
			// 데이터베이스 쿼리를 통해 변경 사항 확인 
			List<String> changes = jdbcTemplate.queryForList("SELECT change FROM changes_table", String.class); 
			if (!changes.isEmpty()) { 
				// 변경 사항이 감지되었을 때 웹훅을 호출 
				notifyWebhook(changes); 
			} 
		}, 0, 1, TimeUnit.MINUTES); // 1분마다 데이터베이스를 폴링 
	} 
	
	// 웹훅을 호출하여 변경 사항을 알림 
	private void notifyWebhook(List<String> changes) { 
		// 웹훅 엔드포인트 URL 
		String webhookUrl = "http://example.com/webhook"; 
		// 웹훅 호출을 위한 요청 헤더 설정 
		HttpHeaders headers = new HttpHeaders(); 
		headers.setContentType(MediaType.APPLICATION_JSON); 
		// 웹훅 호출을 위한 요청 바디 설정 
		HttpEntity<List<String>> request = new HttpEntity<>(changes, headers); 
		// 웹훅 호출 
		restTemplate.exchange(webhookUrl, HttpMethod.POST, request, Void.class);
	} 
}
```