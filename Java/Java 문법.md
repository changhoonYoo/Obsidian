### default

\- example
```java
public interface ServiceOwnerRepository extends ...

default Page<Revision<Integer, ServiceOwner>> findRevisionsByCode(UUID code, Pageable pageable){  
    return findRevisions(a->{  
        return a.add(AuditEntity.property("code").eq(code));  
    }, pageable);  
}
```

`default` 키워드는 자바 8에서 추가된 기능 중 하나로, 인터페이스에 기본 구현을 제공할 수 있도록 합니다. 이렇게 하면 인터페이스를 구현하는 클래스에서 해당 메서드를 오버라이드하지 않아도 되며, 필요한 경우 오버라이드할 수 있습니다.

위의 코드에서 `default` 키워드는 `findRevisionsByCode` 메서드가 `RevisionRepository` 인터페이스에서 기본적으로 제공되는 메서드가 아닌, 해당 인터페이스를 상속받는 클래스에서 추가된 커스텀한 메서드임을 나타냅니다.

즉, `findRevisionsByCode` 메서드는 `RevisionRepository`에 기본적으로 정의되어 있지 않으며, 이 인터페이스를 구현하는 클래스에서 필요한 경우에만 해당 메서드를 구현할 수 있도록 하는데 사용되었습니다. 이렇게 하면 `RevisionRepository`의 다른 메서드들을 구현하는데 필요한 일반적인 기능들을 `default` 키워드를 사용하여 제공할 수 있습니다.