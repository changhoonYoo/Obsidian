## 깃허브 커밋 메세지 형식

깃허브의 커밋 메시지에는 일반적으로 다음과 같은 형식이 권장되고 있습니다. 이 형식은 Angular 커밋 메시지 컨벤션을 따르는 것이며, 읽기 쉽고 일관된 히스토리를 유지하기 위한 목적입니다.


`<타입>(<범위>): <메시지>`

- `<타입>`: 커밋의 목적이나 변화의 종류를 나타내는 부분으로, 다음 중 하나를 선택합니다.
    - `feat`: 새로운 기능 추가 (feature)
    - `fix`: 버그 수정
    - `docs`: 문서 수정
    - `style`: 코드 포맷팅, 세미콜론 누락 등 코드 스타일 관련 수정
    - `refactor`: 코드 리팩토링
    - `test`: 테스트 코드 추가 또는 수정
    - `chore`: 빌드 작업, 패키지 매니저 설정 등의 기타 작업
- `<범위>`: 커밋된 변화의 범위를 나타내는 부분으로, 필수는 아니지만 추가하는 것이 좋습니다.
- `<메시지>`: 커밋의 간결한 설명으로, 명령문으로 작성하며 마침표를 사용하지 않습니다.

예시:

```text
feat(user-auth): add registration API endpoint 
fix(bug): resolve issue with user authentication 
docs(readme): update project documentation 
style(css): format styles according to coding guidelines 
refactor(api): improve error handling in API module 
test(unit): add unit tests for user service 
chore(build): update dependencies in package.json
```

이러한 커밋 메시지의 형식을 사용하면 버전 히스토리를 관리하고 변경 이력을 추적하기가 더 편리합니다.