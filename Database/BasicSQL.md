# SQL
* Structured Query Language
* relational & object oriented data model 모두 사용 가능
* relational model과 달리 multiset 기반으로 동작
* 즉, 중복된 튜플 존재 가능함

<br/>


## SQL as DDL
* 테이블 schema 및 관계 등 메타데이터 조작
* CREATE, ALTER, DROP, TRUNCATE, RENAME 등
* `CREATE` : 테이블, 인덱스, View, 프로시저 등을 생성
* `DROP` : 존재하는 테이블, 인덱스, 뷰, 프로시저 등을 삭제
* `ALTER` : 존재하는 테이블, 뷰 등 객체를 변경
* `TRUNCATE` : 테이블의 데이터를 완전히 제거

### create table
* 테이블의 이름과 각 열의 이름, 타입 지정해 테이블을 생성
* not null 등 constraints 명시 가능
* key 명시 가능

### Integrity
* not null : 열의 값이 null 가질 수없음
* primary key / unique : 열의 값이 unique함
* auto_increment
* default : 값이 주어지지 않은 경우 기본값
* foreign key : relational integrity 만족
* check(P) : 튜플이 조건 P를 만족하도록
* on delete / on update : 참조되는 열이 삭제/수정 시
    * SET NULL : 참조하던 값을 null로 설정
    * CASCADE : 참조하는 행도 함께 삭제/수정
    * RESTRICT : 삭제/수정 불가능하게 함

### View
* `view` : virtual relation
* 개념적으로 쿼리를 실행한 결과를 가지는 가상의 테이블
* 실제로 결과를 계산해서 테이블로 저장하지는 않음
    * 실제로 계산해 저장해 둘 경우, 기반하는 테이블의 값이 바뀌면 이후 뷰를 이용한 쿼리는 올바르지 않음
    * 대신 필요할 때 계산해 사용
* 사용 목적
    * view를 통해 일부 데이터를 가려 보안을 지키기 위함
    * 개인의 필요에 따른 가상의 테이블 정의
* `create view <view-name> <query expr>`

### Basic Query Structure
#### create table
```sql
create table [table_name] ([col_definitions]) [table_options]
```
* column definition : 열의 이름, 타입, constraint 정의
* key clause : key, fk 정의
* table options : storage engine, charset 등

#### alter table
```sql
alter table [talbe_name] [spec]

--[spec]
ADD column [col_name][col_def]
ADD {index|key} [idx_name] [idx_type] (idx_col_name)
DROP column [col_name]
DROP {index|key} [index_name]
CHANGE column [prev_name] [new_name] [col_def]
MODIFY column [col_name] [col_def]
```
* 테이블의 정보를 수정하기 위해 사용
* 열의 추가/수정, 인덱스 및 키의 추가/삭제 등

<br/>

## SQL as DML
* 테이블의 데이터를 조회, 삽입, 수정, 삭제
* SELECT, INSERT, UPDATE, DELETE

### Basic Query Structure
#### select
```sql
select [ALL|DISTINCT] [col_list]
from [table_list]
where [cond]
group by [col|expr|pos]
having [where_cond]
order by  [col|expr|pos] [asc|desc]
limit {[offset,]row_count|row_count}
```

### Join
```sql
select ...
from a natural join b
where ...

--join multiple tables
from a natural join b,c
from (a natural join b) join c using(C_1)
from (a natural join b) join c on (...)

--Outer Join
a natural left outer join b
a natural right outer join b
a natural full outer join b
```
* 3개 이상의 테이블을 조인할 경우 잘못 계산된 결과 얻을 수 있음
* 공통된 attribute가 여러개 발생할 수 있기 때문
* theta join을 통해 join condition 명시

### rename
```sql
select name, salary/12 as monthly_salary

-- min salary 찾기 위한 self join
select distinct T.name
from instructor as T, instructor S
where T.salary > S.salary and S.depth_name = "CS"
```
* `as` 를 이용해 열 및 테이블 이름 변경

### string operations
```sql
where name like "%dar%"
```

* `like` : 문자열의 패턴 감지
* `%` : null을 포함한 임의의 문자열
* `_` : 임의의 문자
* concat, to_upper/lower 등 다양한 기능 지원

### Set Comparison (in, some, all)
```sql
select name
from instructor
where salary > {all | some} (...)

-- in with subquery
where (course_id) in (...)

-- in with constant set
where semester in ("Fall", "Spring")
```
* 비교되는 열과 in/all/some 이후의 절의 타입이 같아야 함

### ordering result tuples
* `order by <attrs> <desc|asc>`
* 쿼리의 결과를 주어진 열들을 기준으로 정렬

### aggregate functions
* avg, min, max 등 열의 값을 이용한 수식 계산
* select문에서 열의 자리에 수식 사용

#### group by
```sql
--erronous query! ID ambiguous
select dept_name, ID, avg(salary)
from instructor
group by dept_name;
```
* `group by` : 주어진 열들의 그룹별로 수식을 계산
* select문에서 수식을 제외한 열은 반드시 group by에 존재하는 열이어야 함

#### having
```sql
select dept_name, avg(salary) as average_salary
from instructor
group by dept_name
having average_salary > 42000
```
* 튜플을 선택하는 `where`과 달리, 조건을 통해 그룹을 선택

#### null values with aggregate
* `count(*)` 제외한 모든 수식은 null값을 무시

### Nested Subqueries
* 쿼리의 where 또는 from문 안에 중첩된 쿼리를 작성
* `derived table` : from 문에 작성한 서브쿼리
* derived table은 반드시 `as`를 통해 alias(이름) 짓기
* 같은 문제를 Join/Cartesian Product 혹은 Subquery로 해결 가능

#### Correlated Subquery
```sql
select course_id
from section as S --correlation name
where semester="Fall" and year=2009 and
    exists (
        select *
        from section as T
        where semester="Spring" and year=2010
        and S.course_id=T.course_id
    );

```
* 바깥쪽 쿼리의 정보를 이용하는 서브 쿼리
* Correlation name : 바깥쪽 쿼리의 정보를 이용하기 위한 이름

<br/>

## SQL as DCL
* 데이터의 사용 권한 관리
* GRANT, REVOKE


## Further Questions
* DELETE vs TRUNCATE vs DROP
* char vs varchar vs text type
* [서브쿼리 vs 조인](https://kimsyoung.tistory.com/entry/SUBQUERY-%EC%99%80-JOIN-%EC%9D%98-%EC%B0%A8%EC%9D%B4-%E4%B8%8A) 각각 언제 써야 하나?
* subquery in from vs where clause?
* Storage Engine?
* db-book ch05 (function, procedure, trigger, ...)