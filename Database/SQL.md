## 그룹 함수

### 설명
테이블의 전체 행을 하나 이상의 컬럼을 기준으로 컬럼값에 따라 그룹화하여 그룹별로 결과를 출력하는 함수이고 복수행 함수라고도 한다.
그룹 함수의 종류에는 COUNT, MAX, MIN, SUM, AVG, STDDEV, VARIANCE 등이 있다.

### 규칙
- 그룹함수는 NULL값이 있는 컬럼은 조회에 포함시키지 않는다. 
- ROW가 없는 테이블에 그룹함수 `COUNT()`를 사용 시 0이 출력되며 `SUM()`를 사용시 NULL 값이 출력된다.
- `COUNT, MAX` 와 `MIN`은 문자, 숫자, 날짜 데이터 모두에게서 사용할 수 있다. 그러나 `AVG SUM, VARIANCE, STDDEV`는 NUMBER만 사용 가능하다. 
-  EXPR이 있는 인수들의 자료 형태는 `CHAR, VARCHAR2, NUMBER, DATE` 형이 될 수도 있다.
### 예제

sum(c) : c의 합을 구함, 그룹 함수를 사용할때는 null값이 있는 컬럼은 조회에 포함시키지 않는다.
`select sum(comm) from emp02;`

`avg(c)`: c의 평균을 구함
	select avg(sal) from emp02;

max(c): c의 최대값을 구함
	select max(sal) from emp02;

min(c): c의 최소값을 구함
	select min(sal) from emp02;

count(c): c의 개수를 구함
	select count(*) from emp02;

1. 숫자함수

 - ABS(n): n의 절대값을 반환함
	select abs(-10) from dual; (10) 

 - CEIL(n) : 소수점 첫째 자리에서 올림하는 함수 (매개 값(i) 설정 불가)
	select ceil(10.31) from dual; (11)

 - FLOOR(n): 소수점 첫째 자리에서 내림하는 함수 (매개 값(i) 설정 불가)
	select floor(10.51) from dual; (10)

 - ROUND(n, i): n을 소수점 i+1번째 자리에서 반올림
	select round(5929.1212) from dual; (5929)
	select round(5929.1212,-1) from dual; (5930)
	select round(5929.1212,-2) from dual; (5900)

 - TRUNC(n,i): n을 소수점 i+1번째 자리에서 버림
	select trunc(5929.1212) from dual; (5929)
	select trunc(5929.1212,-1) from dual; (5920)
	select trunc(5929.1212,1) from dual; (5929.1)

 - POWER(n1, n2): n1을 n2번 거듭제곱한 결과
	select power(5,2) from dual; (25)

 - MOD(n1, n2): n1을 n2로 나눈 나머지 값
	select mod(5,2) from dual; (1)

 - <>,!=,^= :같지 않다 

2. 문자함수

 - INITCAP(char): char의 첫문자(공백과 숫자를 제외한 알파벳 중 첫문자)만 대문자, 나머지는 소문자로 변환
	select initcap('hELLOW world') from dual; (Hellow World)

 - LOWER(char): char을 소문자로 변환
	select lower('HELLOW WORLD') from dual; (hellow world)

 - UPPER(char): char을 대문자로 변환
	select upper('hellow world') from dual; (HELLOW WORLD)

 - CONCAT(char1, char2): char1과 char2를 공백없이 붙여준다
	select concat('hellow ','world') from dual;

 - SUBSTR(char, pos, len): char의 pos번째 문자부터 len길이만큼 잘라서 반환(문자열 자르기)
	select substr('hellow world',4,5) from dual; (low w)

 - SUBSTRB(char, pos, len): SUBSTR과 같음. 다만 len의 기준이 byte수임.
	
 - LTRIM(char1, char2): 왼쪽 공백 제거, char1의 좌측부터 char2를 찾아서 삭제후 반환
	select ltrim('000123','0') from dual; (123)
	
 - RTRIM(char1, char2): 오른쪽 공백 제거, char1의 좌측부터 char2를 찾아서 삭제후 반환 
	select rtrim('123zxc','zxc') from dual; (123)

 - LPAD(char1, n ,char2): char1의 왼쪽부터 char2를 채운다. n은 연산 후 총 문자열 자릿수를 의미한다.
	select lpad('world',12,'hellow ') from dual; (hellow world)
	select lpad('world',10,'hellow ') from dual; (helloworld)

 - RPAD(char1, n ,char2): LPAD와 비슷. 오른쪽부터 진행.
	select rpad('come ',7,'on') from dual; (come on)
	select rpad('come ',6,'on') from dual; (come o)

 - REPLACE(char1, char2, char3): char1에서 char2를 찾아 char3을 반환한다. LTRIM과 달리 여러번을 진행한다.
	select replace('programming','program') from dual; (ming)
	select replace('programming','program','hum') from dual; (humming)

 - LENGTH(char): char의 길이를 반환
	select length('programming') from dual; (11)

 - LENGTHB(char): char의 Byte길이를 반환
	
 - DECODE(a,b,x,y): a=b이면 x 출력, a≠b이면 y출력
     DECODE(a,b,x,c,y,z): a=b이면 x출력, a≠c이면 y출력
			a≠b이고 a≠c이면 z출력
     DECODE(a1,b,DECODE(a2,c,x,y),z): a1=b이면서 a2=c이면 x출력
				    a1=b이면서 a2≠c이면 y출력
				    a1≠b이면 z출력 (DECODE 내부의 DECODE) 
	
3. 날짜함수

 - SYSDATE: 현재 시스템 일자 반환
	select sysdate from dual; (23/06/25)

 - SYSTIMESTAMP: 현재 시스템 타임스탬프 반환
	select systimestamp from dual; (23/06/25 17:44:35.884000000 +09:00)

 - ADD_MONTHS(date, int): date(날짜형변수)에 int 수 만큼 월을 더한 날짜 반환
	select add_months(sysdate,1) from admin; (23/7/25)

 - MONTHS_BETWEEN(date1, date2): date1을 기준으로, 두 날짜 사이의 개월 수 반환
	select months_between(sysdate,add_months(sysdate,-3)) from dual; (3)

 - LAST_DAY(date): date의 월말일을 반환
	select last_day(sysdate) from dual; (23/06/30)

 - ROUND(date, format): format에 따라 반올림한 날짜 반환
	select round(to_date('25-10-23'),'month') from dual; (25/11/01)
	select round(to_date('25-10-21'),'year') from dual; (26/01/01)

 - TRUNC(date, format): format에 따라 잘라낸 날짜 반환
	WITH temp AS (
  	 SELECT TO_DATE(sysdate, 'YYYY-MM-DD HH24:MI:SS') dt, 1234.56 nmb
         FROM dual 
	)  

	SELECT dt 
         , TRUNC(dt, 'DD')   --시간 절사 
         , TRUNC(dt, 'HH24') --분, 초 절사
         , TRUNC(dt, 'MI')   --초 절사
        FROM temp;

	DT 		   | TRUNC(DT,'DD') 	| TRUNC(DT,'HH24')   | TRUNC(DT,'MI')
	2023-06-25 18:18:54| 2023-06-25 00:00:00| 2023-06-25 18:00:00| 2023-06-25 18:18:00

 - NEXT_DAY(date, char): date기준으로 char에 명시한 요일의 날짜를 반환. (기준일의 다음주 요일이 반환)
	select next_day(sysdate,'목요일') from dual; (2023-06-29 18:26:13) (다가오는 목요일)
	select next_day(sysdate-8,'목요일') from dual; (2023-06-22 18:26:57) (전 주 목요일)

4. 변환함수(명시적 형변환)

 - TO_CHAR(char or date, format): 숫자나 날짜를 format에 맞는 문자로 변환
	select to_char(sysdate,'yyyy-mm-dd') from dual; (2023-06-25)
	select to_char(sysdate,'yyyy/mm/dd') from dual; (2023/06/25)

 - TO_NUMBER(data, format) data를 format에 맞는 숫자로 변환(format은 없어도 됨)
	select to_number('12345') from dual; (12345)

 - TO_DATE(char, format): char를 format에 맞는 날짜로 변환.
	select to_date('20230625113519','yyyymmddhh12miss') from dual; (2023-06-25 11:35:19)

 - TO_TIMESTAMP(char, format): char을 format에 맞는 타임스탬프로 변환.
	select to_timestamp('20230625181214','yyyymmddhh24miss') from dual; (23/06/25 18:12:14.000000000)

5. NULL 관련 함수

 - NVL(input1, input2): input1이 NULL이면 input2를 반환한다.
	select nvl(comm,0) from emp (comm이 null이면 0)

 - NVL2(input1, input2, input3): input1이 NULL이면 input2를, 아니면 input3를 반환한다.
	select nvl2(comm,'Y','N') from emp (comm이 있으면 Y, 없으면 N)

 - LNNVL(조건식): LNNVL 함수를 사용하는 이유는 해당 컬럼에 NULL이 존재할 경우 해당 NULL 데이터도 함께 조회되도록 하기 위해서이다.
	LNNVL(comm = 0)
	- comm is null 
	- comm != 0
	select * from emp where lnnvl(comm = 500); (comm이 500인 레코드를 제외 null도 같이 출력)
	
 - NULLIF(input1, input2): input1과 input2가 동일한 값이면 NULL을, 아니면 input1을 반환한다.
	WITH mydb AS (
  	 SELECT 1 AS item_id, 'ORACLE' AS item_desc FROM dual UNION ALL
  	 SELECT 2 AS item_id, 'SQL' AS item_desc FROM dual
	)

	SELECT item_id
     	 , item_desc
     	 , NULLIF(item_desc, 'ORACLE') AS new_item_desc
  	FROM mydb;

	item_id | item_desc | new_item_desc
	1	| ORACLE    | (null)
	2	| SQL	    | SQL

6. 중복 관련

 - UNION (DISTINCT) : 중복 레코드를 제거한다.

 - UNION ALL : 중복 제거를 거치지 않고 결과를 낸다.

--------------------------------------------------------------------------------------------------------------------------------
수업 복습

alter table dept01 modify(dname varchar2(30)); --dname컬럼 크기를 50에서 30으롭 변경

rename dept01 to dept02; --dept01 테이블명을 dept02로 변경

truncate table dept02; --dept02테이블의 모든 레코드를 삭제

select * from tab; --현재 접속중인 사용자가 사용 가능한 테이블 이름을 확인할수 있다.

desc customer; --customer테이블 구조 확인, desc문은 이클립스에서는 실행 안됨.

select ename,comm,sal*12+comm,nvl(comm,0),sal*12+nvl(comm,0) from emp01; --comm이 null인 경우 제대로 값을 구하지 못해 nvl(comm,0)으로 null을 0으로 바꾼다.

select distinct deptno from emp03; --중복 레코드 제거

select * from emp03 where not deptno=10; --부서번호가 10이 아닌 경우 검색

select ename,sal from emp03 where sal NOT between 2000 and 3000; --급여가 2000에서 3000사이가 아닌 사원을 검색

select ename,comm from emp03 where comm in(200,250,500); 
select ename,sal from emp03 where sal not in(2000,1500,2500); -in 연산자 특징 * 컬럼명 in(A,B,C) : 특정 컬럼의 레코드값이 A,B,C 중에 어느 하나만 만족하더라도 검색하는 in연산자이다. 

select ename from emp03 where ename like '홍_동'; --_ 단 하나 모르는 문자와 매핑 대응

select * from emp03 where comm=null; --검색x
select * from emp03 where comm is null; --보너스가 null인 자료를 검색

select empno,ename,sal,comm from emp03 where comm is not null order by empno desc; -- comm is not null

create table emp16 as select * from emp15; --emp15테이블의 모든 컬럼과 레코드를 복제한 emp16테이블 생성
create table emp28 as select empno,ename,sal from emp27 where 10=0; --조건을 거짓으로해서 empno,ename,sal 컬럼 테이블 구조만 복제한 emp28테이블 생성, 레코드는 복제하지 않음

select deptno,avg(sal) from emp02 group by deptno; --부서별 급여 평균

select deptno,max(sal),min(sal) from emp02 group by deptno having max(sal)>2000; --부서별 최대급여가 2000을 초과한 부서번호의 부서 번호,부서별 최대값,최소값을 구함

7. 단일행 서브쿼리문
  서브 쿼리에서 반환되는 레코드 행값이 하나인 것

select dname from dept15 where deptno = (select deptno from emp15 where ename='홍길동'); --단일행 서브쿼리문

select ename,sal from emp15 where sal >(select avg(sal) from emp15); --급여 평균보다 높은 급여를 받는 사원명, 급여 출력

8. 다중행 서브쿼리문
 가. 다중 행 서브쿼리문은 서브 쿼리에서 반환되는 레코드 행값이 하나 이상일 때 사용하는 서브쿼리를 말한다.
 나. 다중 행 서브쿼리문에서는 단일 행 서브쿼리문 연산자(=,>,>=,<,<=,<>)를 사용할 수 없고, 다중 행 서브쿼리문 연산자(in 연산자, >ALL연산자, > any연산자)를 사용해야 한다.
	ANY: 조건을 만족하는 값이 하나라도 있다면
	ALL: 모든 조건을 만족하는 결과

select ename,sal,deptno from emp15 
where deptno =(select distinct deptno from emp15 where sal>=1200); --x

select ename,sal,deptno from emp15
where deptno in(select distinct deptno from emp15 where sal>=1200); --o

create table emp16 as select * from emp15; --emp15테이블의 모든 컬럼과 레코드를 복제한 emp16테이블 생성

8. 조인(join)이란?

ON - JOIN이 실행되기 전

WHERE - JOIN이 실행된 후

 - 하나이상의 테이블을 서로 합쳐서 원하는 데이터를 조회(select문)하기 위해서 사용하는 것을 말한다.

 - cross join이란? 2개이상의 테이블을 조인할 때 where 조건절에 의한 공통되는 컬럼에 의한 결합이 발생하지 않을 때 사용한다. 테이블의 전체 행에 대한 컬럼이 조인된다.
	select * from room,st03;

 - Equi 조인이란?
 *  1. 가장 많이 사용되는 조인 방법으로 조인 대상이 되는 두 테이블에 공통으로 존재하는 컬럼의 값이 
 *  일치되는 행을 연결하여 결과를 만드는 조인 기법이다.
 *  2. 두 테이블을 조인하려면 일치되는 공통 컬럼을 사용해야 한다. 컬럼명이 같으면 혼동이 오기 때문에
 *  공통 컬럼명 앞에 테이블명을 명시해야 한다. 
	select * from room,st03 where room.roomno = st03.roomno;

 - Non Equi 조인이란? where 조건절에서 특정 범위내의 조인 조건으로 검색하는 기법

9. ANSI join이란? 미국 표준협회에서 명확한 키워드명을 사용해서 조인하는 기법을 말한다.

 - cross join
	select * from depart11 cross join student11; -- 쉼표없이 명확한 단어 cross join이라는 것을 사용

 - inner join
	select * from depart11 inner join student11 on depart11.dept_code = student11.dept_code;
	
 - using(c)절 문을 이용한 inner join
	select st_no,st_name,st_gender,dept_code,dept_name from depart11 inner join student11
	using(dept_code); --using절문에는 테이블명 또는 테이블명 별칭을 사용할 수 없다.

 - natural join이란?
 *  조건절을 생략하고 natural join을 사용하면 자동적으로 해당 테이블의 모든 컬럼을 대상으로
 *  공통 컬럼을 조사하여 내부 조인(inner join)을 실행한다.
	select * from depart11 natural join student11;

with check option; : 뷰를 통한 기존 테이블의 컬럼값을 변경 못하게 한다.
with view only; : 기존 테이블의 어떤 컬럼 레코드값도 수정 못하게, 읽기 전용.

select view_name,text from user_views;
view_name : 뷰 이름
text : 쿼리문 내용

alter table emp add(job varchar2(50)); --emp테이블에 job컬럼 추가
desc emp; --테이블 구조 확인
alter table emp modify(job varchar2(20)); --job컬럼 크기 50에서 20으로
alter table emp drop column job; --job컬럼 삭제

truncate table emp21; --emp21 테이블의 모든 레코드 삭제

rename emp21 to test; --emp21 테이블을 test 테이블로 변경

/* 현재 접속중인 사용자(dayjob)로 사용할 수 있는 테이블명을 알고자 하는 경우 */
select table_name from user_tables order by table_name desc;

insert all
into emp28 values(empno,ename,sal)
into emp29 values(empno,ename,LOC)
select empno,ename,sal,LOC from emp27 where deptno=20;
/* emp27 원본 테이블의 20번 부서번호에 해당하는 사원번호, 사원명, 급여, 사는 지역을 검색해서 
 emp28 테이블에는 사원번호, 사원명, 급여를 저장하고, emp29테이블에는 사원번호, 사원명, 사는 지역을 동시에 저장한다.*/
 select * from emp28 order by empno;
 select * from emp29 order by empno asc;

하나 이상 테이블에 다중행 레코드 저장
insert all
into emp28 values(empno,ename,sal)
into emp29 values(empno,ename,LOC)
select empno,ename,sal,LOC from emp27 where deptno=20;

--조건에 맞는 레코드만 복수개의 테이블에 다중행 레코드 저장실습
insert all
when sal >=1000 then --급여가 1000이상인 경우 다중행 레코드 저장
into emp31 values(empno,ename,sal)
when deptno =20 then --부서번호가 20인 경우 다중행 레코드 저장
into emp30 values(empno,ename,deptno)
select empno,ename,sal,deptno from emp27;

update dept01 set (dname,LOC) = (select dname,LOC from dept where deptno=40) where deptno=20;
/* dept테이블의 40번 부서번호의 부서명과 부서지역을 검색해서 dept01테이블의 20번 부서번호의 부서명과
부서지역을 수정하는 서브쿼리를 통한 레코드 수정 실습 */