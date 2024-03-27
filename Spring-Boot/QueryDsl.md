### 설명 및 예제
Querydsl은 Java 언어를 사용하여 SQL과 유사한 쿼리를 작성할 수 있는 오픈 소스 프레임워크입니다. SQL을 작성할 때 자바 코드로 작성할 수 있도록 도와주는 것이 주요 목적입니다. 이를 통해 컴파일러가 쿼리를 검증하고, 코드 자동 완성 기능을 제공하여 개발자가 쿼리 작성 시 발생할 수 있는 오타나 문법 오류를 방지할 수 있습니다.

Querydsl은 다양한 데이터베이스와 호환되며, JPA, Hibernate, JDO, JDBC 등 다양한 ORM 프레임워크와 함께 사용할 수 있습니다. 또한, Querydsl은 동적 쿼리를 작성하는 데 유용한 기능을 제공하며, 쿼리의 재사용성을 높이고, 코드의 가독성을 높이는 데 도움을 줍니다.

- Controller
```java
// 자주묻는 질문  
@GetMapping("/faq")  
@GetMapping("/faq")  
public ResponseEntity<?> getFrequentlyAskedQuestions(@ModelAttribute FrequentlyAskedQuestionRequestDTO request) {  
    try {  
        Pageable pageable =  
                PageRequest.of(  
                        request.getPageNumber(),  
                        request.getPageSize(),  
                        Sort.by(Sort.Order.desc("regDttm")));  
        Page<FrequentlyAskedQuestionResponseDTO> response = 
        customerService.getFrequentlyAskedQuestions(request, pageable);  
        return ResponseEntity.ok(response);  
    } catch (Exception e) {  
        log.error("자주묻는 질문 조회 중 오류 발생", e);  
        return ResponseEntity.internalServerError().body("자주묻는 질문 조회 중 오류 발생");  
    }  
}
```
- Service
```java
public Page<FrequentlyAskedQuestionResponseDTO> getFrequentlyAskedQuestions(FrequentlyAskedQuestionRequestDTO request, Pageable pageable) {  
    QCustomerFaq qCustomerFaq = QCustomerFaq.customerFaq;  
    BooleanBuilder builder = new BooleanBuilder();  
  
    String[] categories = {"2", "3", "5", "14", "16"};  
    if (Arrays.asList(categories).contains(request.getCategory())) {  
        builder.and(qCustomerFaq.ctgId.eq(request.getCategory()));  
    }  
  
    if (StringUtils.hasText(request.getKeywordValue())) {  
        switch (request.getKeywordValue()) {  
            case "title" -> builder.and(qCustomerFaq.title.like("%" + request.getKeyword() + "%"));  
            case "content" -> builder.and(qCustomerFaq.content.like("%" + request.getKeyword() + "%"));  
        }  
    }  
    builder.and(qCustomerFaq.bbsId.eq("faq")).and(qCustomerFaq.secretYn.eq("N"));  
  
    QueryResults<CustomerFaq> queryResults = queryFactory.selectFrom(qCustomerFaq)  
            .where(builder)  
            .orderBy(qCustomerFaq.wrtDttm.desc())  
            .offset(pageable.getOffset())  
            .limit(pageable.getPageSize())  
            .fetchResults();  
  
    List<FrequentlyAskedQuestionResponseDTO> response = queryResults.getResults()  
            .stream()  
            .map(FrequentlyAskedQuestionResponseDTO::new)  
            .toList();  
  
    return new PageImpl<>(response, pageable, queryResults.getTotal());  
}
```

- DTO
```java
@Data  
@NoArgsConstructor  
@AllArgsConstructor  
public class FrequentlyAskedQuestionResponseDTO {  
  
    private long artId;  
    private String category; // <- CustomerFaq ctgId  
    private String title;  
    private String content;  
    @JsonIgnore  
    private String regDttm; // <- CustomerFaq wrtDttm  
  
    public FrequentlyAskedQuestionResponseDTO(CustomerFaq customerFaq) {  
        this.artId = customerFaq.getArtId();  
        this.category = convertCtgIdToString(customerFaq.getCtgId());  
        this.title = customerFaq.getTitle();  
        this.content = customerFaq.getContent();  
        this.regDttm = customerFaq.getWrtDttm();  
    }
 }
```

Controller에서 만든 Pageable 객체에서 `Sort.by(Sort.Order.desc("regDttm"))`는 
마지막에 처리된`List<FrequentlyAskedQuestionResponseDTO>`의 정렬을 담당한다.

실제 데이터베이스의 정렬을 맡는 부분은 
```java
QueryResults<CustomerFaq> queryResults = queryFactory.selectFrom(qCustomerFaq)  
            .where(builder)  
            .orderBy(qCustomerFaq.wrtDttm.desc())  
            .offset(pageable.getOffset())  
            .limit(pageable.getPageSize())  
            .fetchResults();  
```

`orderBy(qCustomerFaq.wrtDttm.desc())` 부분에서 정렬을 한다.

DTO에서 wrtDttm 데이터를 regDttm에 넣었으므로, Pageable의 Sort 속성은 regDttm을 기준으로 정렬시킨다.

---

### fetchResults(), fetchCount() deprecated

QueryDsl 특정버전 이상에서는 fetchResults(), fetchCount() 가 deprecated 되었다.

그러면 앞으로 QueryDsl 로 페이징을 처리할 때는.




