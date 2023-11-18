# Concurrency Control
* 여러 transaction이 동시에 수행될 때 명령의 수행 순서를 결정
* 트랜잭션의 isolation 성질을 만족시키기 위함
* conflict/view serializable한 스케줄
* recoverable한 스케줄
* (optional) casecadeless 한 스케줄

<br/>

## Lock-Based Protocols
* 락을 통해 데이터 아이템에 대한 권한을 얻은 뒤 읽기/쓰기 수행
    * `exclusive lock` : 획득 후 읽기/쓰기 가능. 다른 트랜잭션과 공유하지 않음
    * `shared lock` : 획득 후 읽기 가능. 다른 트랜잭션과 공유 가능
    * request lock 명령을 통해 데이터에 대한 락을 획득
    * request 이후 락을 획득할 때까지 트랜잭션을 진행하지 않고 대기
* 락을 사용하는 것만으로는 serializability를 보장하지 않음
* 락을 요청하고 반환하는 일련의 규칙을 정해 serializability 보장

### Deadlock
* 여러 트랜잭션 간 락을 요청하는 대기의 의존 관계가 사이클을 이뤄 진행되지 않는 상황
* 트랜잭션 중 하나를 선정해 rollback해 데드락을 해소
* `starvation` : 데드락 발생을 해소하고자 같은 트랜잭션을 반복적으로 kill

### Deadlock Handling
#### deadlock prevention
* 구조적으로 데드락이 아예 발생하지 않도록 방지
* 데이터 아이템에 접근하는 partial order를 강제해 순서대로 락을 요청하거나 
* 트랜잭션 실행 전 모든 락을 미리 가져오는 등

#### deadlock avoidance
* 동적으로 데드락이 발생하는지 검사하며 발생할 것 같다면 방지
* `wait-die`
    * non-preemtive. 
    * 우선순위 높은 트랜잭션은 락이 해제되기를 기다림
    * 우선순위 낮은 트랜잭션은 락 획득을 기다려야 하면 롤백
* `wound-wait`
    * preemtive
    * 우선순위 높은 트랜잭션이 락을 가지는 낮은 우선순위의 트랜잭션을 롤백시킴
    * 우선순위 낮은 트랜잭션은 락이 해제되기를 대기

#### detection & recovery
* 데드락이 발생했는지 감지한 뒤 해소하는 방법
* `detection`
    * 트랜잭션이 락을 기다리는 관계를 나타내는 wait-for 그래프 유지
    * 그래프에 사이클이 발생하면 데드락 발생
* `recovery`
    * 원인이 되는 트랜잭션을 선정해 롤백
    * `total` : 트랜잭션을 abort 이후 다시 시작
    * `partial` : 데드락을 발생시킨 지점까지만 트랜잭션의 상태를 되돌림
    * 기아 문제가 발생할 수 있으므로, 우선순위에 고려해야 함

### 2 Phase Locking Protocol
1. Growing Phase
    * 트랜잭션들이 락을 획득하는 단계. 락을 반환하지 않음
2. Shrinking Phase
    * 트랜잭션들이 락을 반환하는 단계. 락을 획득하지 않음
* 2PL을 통해 serializability 보장
    * `lock point` : 각 트랜잭션이 마지막 락을 획득하는 시점
    * lock point 순서의 serial schedule과 conflict serialiable
* 2PL을 사용해도 발생 가능한 deadlock 및 recoverability를 고려해야 함
* `strict 2PL`
    * 트랜잭션 commmit/abort 까지 exclusive lock을 유지
    * recoverability를 보장하고 cascading rollback 발생하지 않음
* `rigorous 2PL`
    * 트랜잭션 commmit/abort 까지 모든 lock을 유지
    * commit의 순서가 equivalent serial 스케줄의 순서
* 성능이 저하되지만, 2PL, strict는 lock point를 알기 어려우므로 rigorous 2PL을 많이 구현함

### Lock Conversion
* `upgrade` : slock을 xlock으로 변환
* `downgrade` : xlock을 slock으로 변환
* 2PL의 1단계에 upgrade, 2단계에 downgrade 섞어 사용해 concurrency 높일 수 있음

### Lock Table
* `lock manager` : lock request, release 요청을 받아 데이터에 대한 락을 관리
* `lock table` : 해시 테이블을 이용해 데이터에 대한 락을 관리하는 자료구조
* 데이터를 해시 키로 받아 각 버킷에 해당 데이터를 사용하는 트랜잭션 및 락의 종류
* 데드락 발생 시 트랜잭션이 가진 락을 모두 해제해야 하므로 역방향 테이블(transaction -> data) 유지

### Graph-Based Lock Protocols
* 데이터 아이템에 대한 접근 순서를 사전에 알 수 있는 경우 사용
* 한 트랜잭션에서 $d_i$ 접근 이후 $d_j$ 접근한다면 $d_i \rightarrow d_j$ 간선을 그래프에 추가
* 즉, 데이터 간의 partial ordering을 나타내는 사이클이 없는 방향 그래프 이용
* 모든 락이 xlock이라 가정하고, 부모 데이터의 락을 가지고 있을 때만 자식 아이템의 락을 허용
* conflict serializable하고, unlock이 2PL보다 빠르게 일어남. 데드락 발생하지 않음
* 부모 락이 필요하기 때문에 동시성이 떨어짐. recoverability와 cascadeless 보장하지 않음

<br/>

## Timestamp-Based Protocols
* 각 트랜잭션이 시작한 시간을 타임스탬프로 기록
* 타임스탬프 순서를 통해 serializability 순서를 결정

### Basic Timestamp Ordering (BTO) Protocol
* `W-timestamp(Q)` : 데이터 Q에 대해 write(Q)를 한 트랜잭션들 중 가장 큰 타임스탬프
* `R-timestamp(Q)` : 데이터 Q에 대해 read(Q)를 한 트랜잭션들 중 가장 큰 타임스탬프
* 충돌이 발생하는 두 명령 간 순서는 타임스탬프 순서로 실행
* Ti가 read(Q) 수행 시
    * $TS(T_i) \le W-TS(Q)$ : Ti를 롤백. 자신보다 늦게 실행되는 트랜잭션의 값을 읽는 것은 모순임
    * $TS(T_i) \ge W-TS(Q)$ : 읽기 동작을 수행 후 R-TS(Q)를 자신의 타임스탬프와 비교해 큰 값으로 업데이트
* Ti가 wriet(Q) 수행 시
    * R-TS(Q)와 W-TS(Q) 모두 자신의 타임스탬프보다 작아야 실행

### BTO summary
* conflict serializability를 보장
* 각 트랜잭션이 대기하지 않으므로 데드락이 발생하지 않음
* cascadeless / recovarable 보장하지 않음

### Thomas' Write Rule
* TS(Ti) < TW-TS(Q) 인 경우 BTO는 롤백하는 반면 write를 무시하고 Ti 실행
* view serializable한 스케줄의 일부 (blind write) 표현 가능

<br/>

## Validation-Based Protocol
1. 모든 쓰기 동작을 트랜잭션의 끝부분으로 미루고 읽고 각 트랜잭션이 읽고 쓴 데이터를 기록
2. 커밋 시점에 validation을 수행해 serialization 가능한지 검사
3. 문제가 없는 경우 수행 결과를 DB에 반영. 아니라면 Ti를 롤백
* 커밋 시점을 serialization 순서로 사용하고자 함
* `optimistic` 동시성 제어 알고리즘임
    * 모든 트랜잭션이 문제 없다고 가정하고 커밋 타임까지 수행
    * 높은 동시성(성능)
    * 다만 크기가 큰(coarse) 트랜잭션이 롤백 시 오버헤드가 큼
    * 대부분 read 동작만 수행하는 트랜잭션일 경우 충돌이 적으므로 유효함
* 락, 타임스탬프, 멀티버전 등 동시성 제어 알고리즘과 병용 가능


<br>

## Multiversion Concurrency Control
* write 동작이 새로운 버전의 데이터를 만듬. 타임스탬프로 버전을 레이블
* read 시 타임스탬프 기반 적절한 버전의 데이터를 선택해 가져옴

### Snapshot Isolation
* timestamp, lock, validation, multiversion 아이디어를 혼합
* 의사결정 쿼리의 성능 개선
    * 통계 정보 구하는 등 정확하지 않아도 괜찮은 질의
    * 다른 `OLTP`(계좌이체 등 변경되는 튜플은 적지만 정확)과 충돌해 성능이 좋지 않음
* lost update 등의 문제 발생 가능

#### read
* 스냅샷을 기반으로 수행. 가장 최근에 커밋된 버전의 데이터 가져옴

#### write
* 결과를 로컬에 쓴 후 validation 단계에서 동시에 수행한 다른 트랜잭션과 비교
* 동일한 write set을 가지는 트랜잭션을 비교
* `first committer win`
    * 먼저 커밋이 나타난 T를 커밋, 아닌 T는 롤백
* `first updater win`
    * 락을 통해 누가 먼저 업데이트했는지 결정
    * 먼저 업데이트한 T를 커밋, 아닌 T는 롤백

#### Benefits & Limitations
* read 동작을 위해 기다리지 않음
* read committed와 비슷한 성능을 보임
* dirty read, non-repeatable read, lost update 발생하지 않음
* serializable 실행을 보장하지는 않음
* write skew 등의 문제점 발생 가능 (서로가 쓴 데이터를 읽은 뒤 쓰는 경우)