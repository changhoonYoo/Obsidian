### 네이티브 쿼리 조회 결과 -> DTO 매핑

- repository
```java
public interface ChatMessageRepository extends JpaRepository<ChatMessage, String> {  
    // 채팅방이름, 최근 메시지 내용, 최근 메시지 시간, 미열람 메시지 건수 조회  
    @Query(nativeQuery = true, value =  
           "SELECT " +  
               "chat_room_id AS room_id, " +  
               "(SELECT snd_msg FROM chat_messages sub WHERE sub.chat_room_id = main.chat_room_id ORDER BY sub.rcv_dttm DESC LIMIT 1) AS latest_msg, " +  
               "MAX(rcv_dttm) AS latest_rcv_dttm, " +  
               "COALESCE(COUNT(CASE WHEN read_yn= 'N' THEN 1 END), 0) AS unread_count " +  
           "FROM chat_messages main " +  
           "WHERE chat_room_id LIKE :number " +  
           "GROUP BY chat_room_id " +  
           "ORDER BY latest_rcv_dttm DESC"  
    )  
    List<Object[]> getChattingRoomData(String number);  
}
```

`List<Object[]>` 매핑

- service
```java
public List<ChattingRoomResponseDTO> getRoom(String userKey, String number) {  
	if(!isSubscribed(userKey, number)) {
	  return null;  
	} else {  
		return chatMessageRepository.getChattingRoomData(number015 + "%")  
        .stream()  
        .map(objArray -> new ChattingRoomResponseDTO(  
                String.valueOf(objArray[0]),  
                String.valueOf(objArray[1]),  
                String.valueOf(objArray[2]),  
                Integer.parseInt(String.valueOf(objArray[3]))  
        ))  
        .collect(Collectors.toList());
	}
}
```

`List<Object[]>` 객체를 .stream().map() 을 통해 DTO 생성자에 Object 배열  값 주입

- - -


### JPQL & QueryMethod

JPQL(Java Persistence Query Language)과 Spring Data JPA에서 제공하는 쿼리 메서드는 서로 다른 것입니다.

1. **JPQL (Java Persistence Query Language):**
    
    - JPQL은 JPA(Java Persistence API)에서 사용하는 쿼리 언어로, 객체 지향적인 방식으로 엔터티를 조회하고 조작하기 위한 언어입니다.
    - JPQL은 SQL과 유사하지만, 테이블이나 컬럼 이름 대신 엔터티 클래스와 필드를 기반으로 쿼리를 작성합니다.
    - 예를 들어, `SELECT e FROM EntityName e WHERE e.fieldName = :paramValue`와 같은 형태의 쿼리를 작성할 수 있습니다.
2. **쿼리 메서드:**
    
    - Spring Data JPA에서는 쿼리 메서드(Query Methods)를 사용하여 데이터베이스에 접근할 수 있습니다.
    - 쿼리 메서드는 메서드의 이름 규칙을 따라 정의된 메서드에 의해 자동으로 쿼리가 생성됩니다. 메서드 이름만으로 간단한 쿼리를 작성할 수 있는 장점이 있습니다.
    - 예를 들어, `findByFieldName(String fieldName)`와 같은 메서드를 정의하면, Spring Data JPA는 해당 메서드에 대한 적절한 쿼리를 자동으로 생성합니다.


```
// Spring Data JPA 쿼리 메서드의 예제 
public interface UserRepository extends JpaRepository<User, Long> {     List<User> findByUsername(String username); 
}
```


따라서 JPQL은 명시적인 쿼리 언어로서 엔터티와 관련된 복잡한 쿼리를 작성하는 데 사용되고, 쿼리 메서드는 간단한 쿼리를 자동으로 생성하기 위한 Spring Data JPA의 편리한 기능입니다. 개발자는 프로젝트의 요구사항에 따라 적절한 방법을 선택하여 사용할 수 있습니다.