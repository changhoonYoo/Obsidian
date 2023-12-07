## <span style="color:red;">그룹 함수</span>

### 설명
테이블의 전체 행을 하나 이상의 컬럼을 기준으로 컬럼값에 따라 그룹화하여 그룹별로 결과를 출력하는 함수이고 복수행 함수라고도 한다.
그룹 함수의 종류에는 `COUNT, MAX, MIN, SUM, AVG, STDDEV, VARIANCE` 등이 있다.

### 규칙
- 1. 그룹함수는 `NULL`값이 있는 컬럼은 조회에 포함시키지 않는다. 
- 2. ROW가 없는 테이블에 그룹함수 `COUNT()`를 사용 시 0이 출력되며 `SUM()`를 사용시 NULL 값이 출력된다.
- 3. `COUNT, MAX` 와 `MIN`은 문자, 숫자, 날짜 데이터 모두에게서 사용할 수 있다. 그러나 `AVG SUM, VARIANCE, STDDEV`는 NUMBER만 사용 가능하다. 
- 4. EXPR이 있는 인수들의 자료 형태는 `CHAR, VARCHAR2, NUMBER, DATE` 형이 될 수도 있다.
