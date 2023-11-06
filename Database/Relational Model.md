# Relational Model
* 주로 사용되는 data model의 일종
* set theory와 predicate logic을 기반으로 둠
* 데이터를 `relation(table)`을 통해 나타냄
* 사용자는 `table`의 `tuple`을 삽입, 삭제하는 등 query 수행


## Relation
* `tuple` : relation이 가지는 데이터 행(row)
* `attribute` : 데이터 테이블의 열(column)
* `domain` : attribute가 가질수 있는 값의 집합
* `atomic` : 도메인의 각 요소가 나눌 수 없는 값을 가짐
* 모든 튜플은 unique함 (서로 다른 값을 가짐)
* relation은 집합에 기반을 두므로 튜플 및 attribute의 순서는 상관없음

### formal definition
$$r(R) \subset D_1 \times D_2 \times ... \times D_n$$
* $D_i$는 i번째 attribute의 도메인
* 각 열이 가질 수 있는 값들의 부분집합

<br/>

## Database Schema
### database schema vs database instance
* database schema : 데이터베이스의 논리적인 설계
* database instance : 한 시점에서의 데이터베이스의 실제 데이터 스냅샷

### relation schema
* relation instance : relation의 한 시점에서의 데이터. 시간에 따라 변할 수 있음. 프로그래밍 언어의 변수 개념
* relation schema : attribute 및 상응하는 도메인들을 가짐. 프로그래밍 언어의 타입 개념

<br/>

## Keys
* 키 $K$는 attributes $R$의 부분집합
* 키를 이용해 각 튜플을 구분
* foreign key를 이용해 다른 relation과 관계를 나타냄

### primary key
* `superkey` : relation의 각 튜플을 키에 포함되는 열만 비교해 unique하게 구분할 수 있음
* `candidate key` : 열의 크기가 가장 작은 superkey
* `primary key` : candidate key 중의 하나를 선택. 
* 주민번호 등 바뀌지 않는 값을 PK로 선정.
* 보통 relation schema를 나타낼 때 PK를 맨 앞에 기술함

### foreign key
* 하나의 relation의 attribute가 다른 relation의 primary key를 가리킴
* 데이터 간의 관계를 나타냄
* 데이터의 중복을 방지
* `referential integrity` : 참조받는 테이블에서 참조하는 테이블의 FK값을 가지는 튜플이 존재해야 함
* relation만으로는 해당 integrity를 표현할 수 없음

<br/>

## Integrity Constraints
* Entity Integrity
    * PK의 값은 null이 될 수 없다
    * PK를 통해 튜플을 식별해야 하기 때문
* Referential Integrity
    * FK를 통한 참조는 참조되는 테이블에서 존재하는 튜플의 값에 대해 이루어져야 함
* Domain Integrity
    * 튜플의 각 열의 값은 반드시 attribute의 도메인 범위 안에서 존재해야 함
    * 타입에 대한 기본값이 주어지지 않았다면 NULL을 값으로 가질 수 있음
* User Defined Integrity
    * 비즈니스 요구 사항에 의해 정의