# Transaction
* DB 데이터에 접근하는 작업을 수행하는 단위

## ACID property
### Atomicity
* `all or nothing`
* 트랜잭션이 완전히 수행된 결과가 저장되거나 아예 반영되지 않아야 함
* failure가 발생하더라도 트랜잭션의 중간까지 실행한 inconsistent한 결과를 반영하지 않아야 함

### Consistency
* 데이터가 올바른 상태를 유지해야 함
* 예를 들어, 계좌 간 송금 후 두 계좌의 금액의 합이 일정해야 함
* PK, FK 등의 키 제약조건과 비즈니스 로직으로 정한 integrity constraint 지켜야 함
* 트랜잭션 수행 중간에는 일시적으로 inconsistent한 상태 가질 수 있으나,트랜잭션 전후로는 consistency 유지해야 함

### Isolation
* 동시에 여러 트랜잭션을 실행해도 각 트랜잭션은 개별로 실행한 것과 같은 결과를 내야 함
* 즉, 트랜잭션을 serial한 순서대로 실행한 것과 같은 결과

### Durability
* 트랜잭션을 실행한 결과가 DB에 영구적으로 저장되어야 함
* DB failure 발생하더라도 데이터가 영구적으로 유지


<br/>

## Transaction State
* `active` : 트랜잭션이 실행 중
* `partially committed` : 트랜잭션의 마지막 구문이 실행된 단계 
* `failed` : 트랜잭션의 정상적인 실행이 불가능한 것을 발견
* `aborted` : 트랜잭션이 실행되기 이전의 상태로 rollback. abort 이후 트랜잭션을 kill하거나 다시 실행
* `committed` : 성공적으로 트랜잭션이 실행되고 결과가 DB에 반영됨

<br/>

## Schedule
* 여러 트랜잭션이 동시에 수행될 때, 트랜잭션의 instruction 실행된 순서
* `serial` : 한 번에 하나의 트랜잭션이 순서대로 실행
* `equivalent` : 두 스케줄을 실행한 결과가 같다면 두 스케줄은 equivalent
* `concurrent` : throughput과 response time을 위해 여러 스케줄의 구문을 동시에 실행


<br/>

## Serializability
* `serializable` : serial 스케줄과 equivalient 하다면 스케줄은 serializable
* 간단화를 위해 트랜잭션의 구문을 read/write로 구분해 각 동작의 conflict를 고려

### conflicting instructions
* `conflict` : 다른 트랜잭션의 구문 간 같은 데이터를 참조하는 경우
    * 같은 데이터를 참조하는 구문의 순서를 바꾸면 결과가 달라짐
    * read-write conflict, write-write conflict 존재

### Conflict Serializability
* `conflict equivalent` : 두 스케줄이 conflict가 발생하지 않는 구문들의 순서를 바꿔 일치함
* `conflict serializable` : 스케줄이 serial 스케줄과 conflict equiavalent할 때

### View Serializability
* 각 데이터 Q에 대해서 다음 조건을 만족하면 스케줄 S와 S'은 `view equivalent`
    * S에서 Ti가 Q의 초기값을 읽으면 S'에서도 Ti가 Q의 초기값 읽음
    * S에서 Tj가 write(Q)로 생성한 Q값을 Ti가 읽는다면 S'에서도 동일하게 읽음
    * S에서 마지막 write(Q)를 한 트랜잭션과 S'에서 마지막 write(Q)를 한 트랜잭션이 동일
* `view serializable` : 스케줄이 serial 스케줄과 view equivalant한 경우

### View vs Conflict Serializability
* 모든 conflict serializable 스케줄은 view serializable하다
* view serializable 하지만 conflict serializable 하지 않은 스케줄은 blind wirte 포함
    * write의 결과가 다른 write에 의해 영향을 주지 않는 경우
* view, conflict serializability를 통해 모든 serializable 스케줄을 나타내지 못함
    * 즉, view, conflict serializable 하지 않지만 serializable한 스케줄 존재
    * write, read 뿐만 아니라 다른 동작을 고려해 분석해야 찾을 수 있음

### Testing Conflict Serializability
* 트랜잭션 간의 `precedence graph` 작성해 검사
    * 각 정점은 트랜잭션
    * 각 간선은 트랜잭션의 구문 간 conflict 발생 시 추가
* 그래프에 사이클이 없다면 conflict serializable
* `toposort`를 통해 트랜잭션의 serial 스케줄을 얻을 수 있음

### Testing View Serializability
* 그래프만으로는 view serializability 검사 불가능함
* NP-complete 문제! 효율적인 알고리즘 찾지 못함
* 대신 view serializable 하기 위한 충분조건 몇 가지를 검사하는 등 휴리스틱

<br/>

## Recoverable Schedule
* 시스템 failure 발생해도 이전 상태로 복원할 수 있는 스케줄
* $T_i$가 write한 데이터를 $T_j$가 읽었다면 커밋 순서도 $T_i$가 먼저인 스케줄

### Cascading Rollback
* $T_i$가 write한 데이터를 다른 여러 트랜잭션이 읽어 간 뒤 $T_i$가 fail하면 읽어간 다른 모든 트랜잭션도 롤백
* 트랜잭션 failure 발생 시 다른 트랜잭션이 수행한 수많은 작업을 롤백해야 할 수 있음
* 

### Casecadelss schedule
* cascading rollback이 발생하지 않는 스케줄
* $T_i$가 write한 데이터를 커밋한 이후 $T_j$가 읽는 스케줄
* 스케줄의 concurrency는 떨어지지만 트랜잭션 fail 발생해도 cascading rollback 하지 않음
* 모든 cascadeless 스케줄은 recoverable

<br/>

## Concurrency Control
* 여러 트랜잭션을 동시에 수행하는 순서를 관리하는 알고리즘
* 스케줄이 다음과 같은 성질을 만족하도록 유지
    * conflict 또는 view serializable 해야 함
    * recoverable 해야 함
    * 가능하다면 cascadeless (필수는 아님)
* on-line concurrency control 필요
    * 트랜잭션을 실행 후 serializable한지 검사하는 것은 너무 느리다!
    * 대신 실행하는 동시에 검사

<br/>

## Consistency Level
### 발생할 수 있는 문제
#### Dirty Read
* 다른 트랜잭션이 커밋하지 않은 데이터를 읽을 수 있음

#### Non-repeatable Read
* 한 트랜잭션에서 테이블의 같은 행에 두 번 이상 접근했는데 그 값이 다른 경우

#### Phantom read
* 트랜잭션 내에서 동일한 쿼리를 보냈을 때 결과가 다른 경우
* 예를 들어 나이가 12 이상인 튜플을 가져오는데 튜플의 내용이나 개수 등이 다른 경우

### Consistency Levels
#### Serializable
* serial 스케줄과 동일한 결과를 가지는 스케줄
* 여러 트랜잭션이 같은 데이터 행에 동시에 접근할 수 없음
* 성능이 가장 떨어짐

#### Repeatable Read
* commit된 레코드만 read 가능함
* 같은 레코드에 대해 여러 번 읽으면 같은 값을 리턴함
* 즉, 하나의 트랜잭션이 수정한 행을 다른 트랜잭션이 수정할 수 없도록 막음

#### Read Committed
* commit된 레코드만 read 가능함
* 같은 데이터에 대해 여러 번 읽었을 때 값이 다를 수 있음
* 보편적으로 가장 많이 사용되는 격리 수준

#### Read Uncommitted
* 커밋되지 않은 레코드를 읽을 수 있음
* 정확도는 떨어지지만, 성능이 가장 좋음
* 통계 정보를 수집하는 등 정확도가 떨어져도 되는 작업에 수행

### Summary
||Dirty read|Non-repeatable read|Phantoms|
|--|--|--|--|
|**Read Uncommitted**|Y|Y|Y|
|**Read Committed**|N|Y|Y|
|**Repeatable Read**|N|N|Y|
|**Serializable**|N|N|N|