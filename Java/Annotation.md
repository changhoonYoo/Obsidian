
<span style="color:orange">@RestResource</span>
`@RestResource(exported = ...)`는 Spring Data REST에서 사용되는 애노테이션 중 하나입니다. 이 애노테이션은 리소스를 노출할지 여부를 결정하는 데 사용됩니다.

기본적으로 Spring Data REST는 JPA 리포지토리의 메서드를 기반으로 RESTful 엔드포인트를 자동으로 생성합니다. 그러나 모든 리포지토리 메서드를 노출할 필요는 없으며, 특정 메서드만 노출하거나, 또는 특정 메서드를 제외하고 나머지를 노출하지 않을 수 있습니다.

- (<span style="color:9999ff">exported</span> = true)
- 