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