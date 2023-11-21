# Recovery System
* atomicity, durability 보장

<br/>

## DB Failure Classification
### Transaction failure
* `logical err` : 트랜잭션 내부 원인으로 트랜잭션을 완료할 수 없는 경우
    * 예를 들어 송금 트랜잭션을 잔고가 부족해 수행할 수 없는 등
* `system err` : DBMS가 실행 중인 트랜잭션을 종료시켜야 하는 경우
    * deadlock 등의 원인

### System Crash
* 전력, 하드웨어, 소프트웨어 등 원인으로 DBMS가 동작할 수 없는 경우
* `fail-stop` : 시스템 충돌 발생해도 non-volatile 스토리지 내용은 보존되어야 함

### Disk Failure
* 디스크 헤드 등 하드우어 상의 문제로 데이터의 손실이 발생
* 체크섬을 통해 디스크 이상 여부를 검사할 수 있음

<br/>

## Storage Structure
* `volatile` storage : 전력 공급이 끊기면 데이터가 보존되지 않는 스토리지
* `non-volatile` storage : 전력 공급이 끊겨도 데이터가 보존되는 스토리지
* `buffer` : 디스크의 내용을 블럭 단위로 가져와 버퍼에서 작업. 버퍼 매니저가 관리

### Stable Storage
* failure가 발생하지 않는 안전한 스토리지는 존재하지 않음
* redundancy : 데이터를 중복으로 저장해 안정성을 확보
    * RAID 시스템 등 데이터를 중복으로 저장하는 스토리지 시스템
    * 체크섬 등을 을 활용한 에러 데이터의 복구

### Data Access
* 블럭 단위로 디스크의 데이터 읽기/쓰기 수행
* 디스크의 블럭을 메모리 버퍼로 가져와 작업을 수행
* 각 트랜잭션이 버퍼 블럭의 데이터 아이템에 대해 작업하는 로컬 메모리 영역 존재

#### Operations
* `input(B)` : 블럭 B를 디스크에서 메모리 버퍼로 가져옴
* `output(B)` : 메모리 버퍼의 블럭 B를 디스크에 작성
* `read(X)` : 트랜잭션 로컬 영역에 데이터 X의 값을 읽음
* `write(X)` : 버퍼 블럭의 데이터 X에 트랜잭션이 작업한 값을 작성

<br/>

## Recovery & Atomicity
* failure 발생해도 atomicity를 보장하기 위한 recovery 기법들

### log-based recovery
* 변경 사항을 DB에 반영하기 전 stable storage에 변경 사항을 먼저 기술

### shadow-copy
* 변경사항을 in-place 변경하지 않고 새로운 복사본을 만들어 저장함
* 변경사항이 완전히 작성되면 포인터를 옮기고 이전 데이터를 삭제
* 텍스트 편집기 등에서 사용하는 기법

<br/>

## Log-Based Recovery
* 로그 레코드 : 로그를 구성. 변경사항에 대한 정보를 포함함
* 변경사항(로그)를 시점 순서대로 추가함. 삭제 및 수정은 불가능
* `<Ti start>` : 트랜잭션 Ti가 시작
* `<Ti, X, V1, V2>` : Ti에서 X의 값을 V1에서 V2로 작성
* `<Ti commit>` : Ti가 모든 동작을 수행 완료함


### immediate vs deferred-modification
* `immediate` : 커밋하지 않은 트랜잭션의 변경 사항을 버퍼나 디스크에 반영 가능
* `deferred` : 커밋 시점에만 변경사항을 반영 가능
* 데이터 작성 전 반드시 로그 레코드를 먼저 작성

### Transaction Commit
* commit 로그 레코드가 stable 스토리지에 저장된 시점을 커밋으로 정의함

### Undo & Redo Operation
* `undo(Ti)`
    * start 로그 레코드는 있지만 commit/abort 로그 레코드는 없는 경우 실행
    * Ti의 마지막 로그 레코드로부터 거꾸로 올라가며 수행
    * `<Ti, X, V1, V2>` 에 대해 이전의 값을 다시 작성 후 로그 레코드 `<Ti, X, V1>` 작성
    * undo 완료 시 `<Ti abort>` 로그 레코드 작성
* `redo(Ti)`
    * start와 commit/abort 로그 레코드가 모두 있는 경우 실행
    * Ti의 모든 동작을 순방향으로 수행
    * undo와 달리, 추가적인 로그 레코드를 작성하지 않음

<br/>

## DB Buffering
### Log Buffer
* 로그 레코드를 메인 메모리 내부의 버퍼에 저장
* 로그 레코드 블럭이 꽉 차면 블럭을 stable storage에 작성
* 혹은 log force에 의해 강제로 블럭을 stable storage에 작성
* 반드시 로그 레코드를 작성한 **순서대로** stable storage에 작성되어야 함
* `WAL` (write-ahead logging) : 버퍼의 데이터 블럭을 DB에 반영하기 전 관련된 로그를 먼저 stable storage에 작성

### DB Buffer
* 디스크 블럭의 내용을 메모리의 버퍼에 가져와 작업
* 실제 메인 메모리 공간, 혹은 가상 메모리, swap space로 구현
* `no-force policy` : 트랜잭션 커밋 시점에 변경사항이 디스크에 반영되지 않아도 됨
    * 버퍼 매니저가 디스크에 작성할 블럭을 선정 및 관리함
* `force policy` : 커밋 시점에 변경사항이 반드시 디스크에 반영되어야 함
* `steal policy` : 커밋되지 않은 트랜잭션의 변경사항이 디스크에 반영될 수 있음
    * 버퍼 공간 부족 등의 이유
    * recovery 알고리즘이 복잡해짐

#### output(B)
1. 다른 트랜잭션이 변경하지 못하도록 B에 대한 latch(lock) 획득
2. log flush (관련된 로그를 stable storage에 저장)
3. 블럭을 디스크에 저장
4. latch 해제

<br/>

## Checkpointing
* DB failure는 자주 발생하지 않으므로 굉장히 많은 양의 로그 레코드가 쌓이게 됨
* 복구 시마다 모든 로그 레코드에 undo/redo 수행 시 많은 계산량 요구함
* 체크포인트를 통해 복구 계산량을 줄임
    1. 모든 로그 레코드를 stable 스토리지로 옮김
    2. 모든 변경된 버퍼 블럭을 디스크로 옮겨 작성함
    3. `<checkpoint L>` 로그 레코드를 stable 스토리지에 작성함
    * 체크포인트 작성 중에는 트랜잭션 수행을 멈춤

<br/>

## Recovery Algorithm
### Logging Phase (평상시 동작)
* start, write, commit 로그 레코드 작성

### Redo Phase
1. 마지막 체크포인트 레코드 `<checkpoint L>` 탐색. undo-list를 L로 설정
2. 체크포인트로부터 순방향으로 탐색하며 수행
    * write 로그 레코드에 대해 쓰기 동작을 다시 수행
    * `<Ti start>` 로그 레코드 발견 시 Ti를 undo-list에 삽입
    * `<Ti commit/abort>` 로그 레코드 발견 시 Ti를 undo-list에서 삭제

### Undo Phase
1. 맨 끝에서부터 거꾸로 로그 레코드를 순회하며 수행
    * undo-list에 포함된 Ti에 대해 `<Ti, X, V1, V2>` 발견 시
        * X에 V1 값을 쓰고 (undo) 
        * `<Ti, X, V1>` 로그 레코드 작성
    * undo-list에 포함된 Ti에 대해 `<Ti start>` 로그 레코드 발견 시
        * `<Ti abort>` 로그 레코드 작성
        * Ti를 undo-list에서 제거
    * undo-list가 빌 때까지 올라가며 수행