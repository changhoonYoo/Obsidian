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

PQL은 SQL과 비슷한 구문을 가지고 있지만, 테이블이나 컬럼 이름 대신에 엔터티 클래스와 엔터티 필드를 대상으로 쿼리를 작성합니다. 아래는 간단한 JPQL 쿼리의 예제입니다:

1. **모든 엔터티 조회:**
    
    - 모든 엔터티를 조회하는 간단한 JPQL 쿼리입니다.
    
    `SELECT e FROM EntityName e`
    
2. **조건을 포함한 조회:**
    
    - 특정 조건을 가진 엔터티를 조회하는 예제입니다.
    
    `SELECT e FROM EntityName e WHERE e.fieldName = :paramValue`
    
3. **연관 관계 활용:**
    
    - 연관 관계를 활용하여 엔터티를 조회하는 예제입니다.
    
    `SELECT e FROM EntityA e JOIN e.relatedEntityB b WHERE b.fieldName = :paramValue`
    
4. **집계 함수 사용:**
    
    - COUNT와 같은 집계 함수를 사용하여 특정 조건을 만족하는 엔터티의 개수를 조회하는 예제입니다.
    
    `SELECT COUNT(e) FROM EntityName e WHERE e.fieldName = :paramValue`
    
5. **정렬 사용:**
    
    - ORDER BY를 사용하여 결과를 정렬하는 예제입니다.
    
    `SELECT e FROM EntityName e ORDER BY e.fieldName ASC`
    

이렇게 JPQL은 SQL과 유사하지만, 엔터티와 필드를 기반으로 한 객체 지향적인 쿼리 언어로, 객체와 관계형 데이터베이스 간의 매핑을 지원하며 JPA에서 사용됩니다. 실제 사용되는 JPQL은 프로젝트의 엔터티 구조와 요구사항에 따라 다양하게 작성될 수 있습니다.

- - -


JPQL(Java Persistence Query Language)과 Spring Data JPA에서 제공하는 쿼리 메서드는 서로 다른 것입니다.

1. **JPQL (Java Persistence Query Language):**
    
    - JPQL은 JPA(Java Persistence API)에서 사용하는 쿼리 언어로, 객체 지향적인 방식으로 엔터티를 조회하고 조작하기 위한 언어입니다.
    - JPQL은 SQL과 유사하지만, 테이블이나 컬럼 이름 대신 엔터티 클래스와 필드를 기반으로 쿼리를 작성합니다.
    - 예를 들어, `SELECT e FROM EntityName e WHERE e.fieldName = :paramValue`와 같은 형태의 쿼리를 작성할 수 있습니다.
2. **쿼리 메서드:**
    
    - Spring Data JPA에서는 쿼리 메서드(Query Methods)를 사용하여 데이터베이스에 접근할 수 있습니다.
    - 쿼리 메서드는 메서드의 이름 규칙을 따라 정의된 메서드에 의해 자동으로 쿼리가 생성됩니다. 메서드 이름만으로 간단한 쿼리를 작성할 수 있는 장점이 있습니다.
    - 예를 들어, `findByFieldName(String fieldName)`와 같은 메서드를 정의하면, Spring Data JPA는 해당 메서드에 대한 적절한 쿼리를 자동으로 생성합니다.


```java
// Spring Data JPA 쿼리 메서드의 예제 
public interface UserRepository extends JpaRepository<User, Long> {     
	List<User> findByUsername(String username); 
}
```


따라서 JPQL은 명시적인 쿼리 언어로서 엔터티와 관련된 복잡한 쿼리를 작성하는 데 사용되고, 쿼리 메서드는 간단한 쿼리를 자동으로 생성하기 위한 Spring Data JPA의 편리한 기능입니다. 개발자는 프로젝트의 요구사항에 따라 적절한 방법을 선택하여 사용할 수 있습니다.

---

### Pagination

- Pageable 객체 설정

```java
Pageable pageable = PageRequest.of(request.getPageNumber(), request.getPageSize(), Sort.by(Sort.Direction.DESC,"sndDttm"));
```

- List<> 2개의 객체를 하나의 Page<>로

```java

public Page<ResultByNumberResponseDTO> getSendResultByNumber(String userKey, String number, Pageable pageable) throws IOException {  
    List<ASend> aSends;  
    List<BSend> bSends;  
    if (number.equals("all")) {  
        String oneMonthBeforeDate = CurrentTime.getMonthBeforeDate(1,"yyyyMMdd");  
        // 최근 한달 데이터만 가져오기  
        aSends = aSendRepository.findByUserKeyAndRsltValAndDelYnAndSndDttmAfter(userKey, -100, "N", oneMonthBeforeDate);  
        bSends = bSendRepository.findByUserKeyAndRsltValAndDelYnAndSndDttmAfter(userKey, -100, "N", oneMonthBeforeDate);  
    } else {  
        aSends = aSendRepository.findByUserKeyAndRcvPhnIdAndRsltValAndDelYn(userKey, number, -100, "N");  
        bSends = bSendRepository.findByUserKeyAndRcvPhnIdAndRsltValAndDelYn(userKey, number, -100, "N");  
    }  
    List<ResultByNumberResponseDTO> resultList = new ArrayList<>();  
    resultList.addAll(convertXmsSendsToResultByNumberResponseDTOs(aSends));  
    resultList.addAll(convertSmtSendsToResultByNumberResponseDTOs(bSends));  
  
    resultList.sort(Comparator.comparing(ResultByNumberResponseDTO::getSndDttm).reversed());  
    int totalElements = resultList.size();  
  
    int start = (int) pageable.getOffset();  
    int end = Math.min((start + pageable.getPageSize()), resultList.size());  
    return new PageImpl<>(resultList.subList(start, end), pageable, totalElements);  
}

```

- `int end = Math.min((start + pageable.getPageSize()), resultList.size());`

`resultList`라는 리스트가 있고, 페이지 번호(`start`)와 페이지 크기`pageable.getPageSize()`가 주어진 상황에서 현재 페이지의 마지막 항목 인덱스(`end`)를 계산하는 것입니다.

여기서 `start`는 현재 페이지의 첫 번째 항목의 인덱스이고, `pageable.getPageSize()`는 한 페이지에 표시할 항목의 수입니다. 따라서 현재 페이지의 첫 번째 항목부터 시작하여 페이지 크기만큼의 항목을 포함하는 부분 리스트를 가져옵니다.

그러나 만약 `resultList`의 크기가 현재 페이지의 마지막 인덱스(`start + pageable.getPageSize()`)보다 작다면, 전체 리스트 크기가 현재 페이지의 범위를 벗어나므로 `resultList`의 크기가 현재 페이지의 마지막 인덱스로 설정됩니다.

이렇게 하면 페이지네이션을 구현할 때 현재 페이지의 범위를 벗어나지 않도록 보장할 수 있습니다. 예를 들어, 10개의 항목이 있는 리스트가 있고 현재 페이지가 2이며 한 페이지당 5개의 항목을 표시한다면, `start`는 5이고 `end`는 10이 될 것입니다.


-  `return new PageImpl<>(pageContent, pageable, totalElements);`

여기서:

`public PageImpl(List<T> content, Pageable pageable, long total)`

- `content`: 현재 페이지의 항목 리스트입니다.
- `pageable`: 페이지 정보를 나타내는 `Pageable` 객체입니다.
- `total`: 전체 항목 수입니다.

`resultList.subList(start, end)`는 `resultList`에서 `start`부터 `end-1`까지의 부분 리스트를 반환합니다. 이것은 현재 페이지에 표시할 항목들을 의미합니다.

따라서 `return new PageImpl<>(resultList.subList(start, end), pageable, totalElements);`는 다음과 같은 내용을 포함하는 새로운 `PageImpl` 객체를 생성하여 반환합니다:

- 현재 페이지의 항목 리스트: `resultList`에서 `start`부터 `end-1`까지의 부분 리스트
- 페이지 정보: `pageable`
- 전체 항목 수: `totalElements`
- 
---


### 두 테이블의 count 데이터 합치기

```java
public List<FrequentlySentDTO> getFrequentlySent(String userKey) {  
    String oneMonthBeforeDate = CurrentTime.getMonthBeforeDate(1, "yyyyMMdd");  
    List<FrequentlySentDTO> aSends = ASendRepository.findRcvPhnIdOrderByCountDesc(userKey, oneMonthBeforeDate)  
            .stream()  
            .map(row -> convertFrequentlySentDTO(row, userKey))  
            .filter(Objects::nonNull)  
            .toList();  
    List<FrequentlySentDTO> bSends = BSendRepository.findRcvPhnIdOrderByCountDesc(userKey, oneMonthBeforeDate)  
            .stream()  
            .map(row -> convertFrequentlySentDTO(row, userKey))  
            .filter(Objects::nonNull)  
            .toList();  
    List<FrequentlySentDTO> combinedList = Stream.concat(aSends.stream(), bSends.stream())  
            .toList();  
  
    return combinedList.stream()  
            .sorted(Comparator.comparing(FrequentlySentDTO::getCount).reversed())  
            .limit(4)  
            .toList();  
}  
  
private FrequentlySentDTO convertFrequentlySentDTO(Object[] row, String userKey) {  
    String rcvPhnId = (String) row[0];  
    int count = ((Number) row[1]).intValue();  
    List<Buddy> buddies = buddyRepository.findByKeyCommNoAndUsrKey(rcvPhnId, userKey);  
    if (!buddies.isEmpty()) {  
        Buddy buddy = buddies.get(0);  
        return new FrequentlySentDTO(buddy.getBuddyNm(), buddy.getBuddySeqNo(), buddy.getKeyCommNo(), count);
    }  
    return null;
}
```

---

### 특정 컬럼 데이터 추출

가져오고자 하는 컬럼만으로 이루어진 Mapping 인터페이스를 만들어서 사용

예제

- Mapping Interface
```java
public interface UserInfoMapping {
	String getName();
	int getAge();
}
```

- repository
```java
public interface UserRepositroy extends JpaRepository<User, Long> {
	List<UserInfoMapping> findAllById(Long id);
}
```