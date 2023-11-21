# Physical Storage System
* volatile vs non-volatile
* 스토리지 선택 시 고려사항
    * 접근속도
    * 단위 용량당 비용
    * reliablity

## Storage Hierarchy
### primary storage
* cache, main memory
* volatile, fast access

### secondary storage
* flash memory, magnetic disk 등
* on-line storage라고 불리기도 함 (항상 접근 가능)

### tertiary storage
* non-volatile, slow access
* magnetic tape, optical storage 등
* magnetic tape : sequential access. 임의 위치 바로 볼 수 없음

### storage interfaces
* SATA
* SAS
* NVMe
* Storage Area Network (SAN) : 네트워크를 통해 저장장치에 연결
* Network Attached Storage (NAS) : 네트워크로 연결된 파일 시스템 인터페이스
* Cloud Storage : 클라우드 서버에 데이터 저장

<br/>

## Magnetic Disk
* **platter** : 데이터를 저장하는 원판. platter를 쌓아 디스크를 구성
* **spindle** : 각 platter가 spindle을 축으로 회전
* **track** : platter를 원형 트랙으로 구분
* **sector** : 읽기/쓰기의 기본 단위. 기본 512바이트. 섹터로 트랙을 구성
* **RW head** : 헤드를 통해 섹터의 데이터에 접근

### Disk Controller
* 디스크에 접근하기 위한 인터페이스
* high level 명령을 받아 섹터로 읽기/쓰기 수행
* 각 섹터에 체크섬을 추가해 데이터가 올바른지 검사
* 쓰기 후 섹터를 다시 읽어 바르게 써 졌는지 검사
* remapping 수행 : bad sector를 새 위치로 옮김

### Performance of Disks
* **Access time** : request 이후 동작 수행 시점까지의 시간
    * **Seek time** : arm을 움직여 올바른 track 찾는 시간
    * **Rotational delay** : 회전을 통해 올바른 sector 찾는 시간
* **Data Transfer Rate** : 데이터 읽기/쓰기에 걸리는 시간
* **MTTF** (Mean Time To Failure) : 디스크가 failure 없이 동작하는 평균 기간
    * 주로 3~5년
    * 수리 시마다 줄어듬
* sequential vs random access
    * random access는 매번 위치를 찾는 access time 필요하므로 느림
* disk block
    * 스토리지 접근의 논리적인 단위. sector 크기의 배수 형태
* IOPS : 디스크가 지원하는 초당 발생하는 random block read의 수

### 블럭 접근 최적화
* Bufering : 메모리 버퍼를 캐시처럼 사용
* Read Ahead : 요청받은 데이터에 추가적인 블럭을 미리 읽어 둠
* Disk Arm Scheduling : 블럭 요청을 기반으로 arm을 최소한으로 움직이는 순서
    * elevator algorithm

<br/>

## Flash Storage (SSD)
### NAND Flash
* 페이지 단위로 읽기/쓰기 수행
* 랜덤 접근과 순차 접근의 차이 크지 않음
* 페이지는 한 번만 쓰일 수 있음. (rewriet 시 지워야 함)

### Erase Operation
* erase는 블럭 단위로 수행
* remapping 통해 논리적 페이지 주소를 실제 페이지 주소로 매핑해 erase 기다리지 않게
* Flash Translation Table을 통해 remapping 수행
* wear leveling : 일정 횟수 이상 erase 수행 시 블럭이 unreliable

### SSD Performance
* 병렬 읽기를 지원
* hybrid disks : 하드디스크에 작은 SSD를 사용해 cache처럼 사용

<br/>

## RAID
* 여러 디스크를 병렬 접근해 높은 용량과 빠른 속도 제공
* 데이터를 중복으로 저장해 reliable
* 단일 디스크 시스템에 비해 MTTF가 빠른 단점 존재 => redundancy 필요

### 병렬화를 통한 성능 향상
* throughput 증가 : load balance 통해 단위 시간당 작업량 증가
* response time 감소 : 단일 작업에 대해 병렬 접근으로 시간 단축
* strpng 통해 데이터 읽기/쓰기 시간 단축
    * bit-level : 각 바이트의 비트들을 여러 디스크로 쪼개어 저장
    * block-level : 데이터를 블록 단위로 쪼개어 여러 디스크에 저장

### RAID Levels
#### Level 0
* block striping
* non redundant
* data loss가 있어도 상관 없이 고성능이 필요한 경우 사용

#### Level 1
* block striping
* mirrored disk : 쓰기 시 데이터를 복사해 mirror disk에도 쓰기
* 쓰기 성능이 가장 좋음
* DB 로그 등 신뢰성 필요한 경우
* random/small update가 많은 앱에서 사용

#### Parity blocks
* 각 디스크의 i번째 블럭의 XOR을 계산해 parity block[i] 계산
* 블럭의 데이터를 복구하기 위해 자신을 제외한 모든 블럭을 XOR

#### Level 5
* block-interleaved distributed parity
* parity를 1개의 디스크에만 저장하지 않고 모든 디스크에 나누어 저장
* 쓰는 데이터 블럭과 parity 블럭이 다른 디스크라면 병렬 쓰기 가능
* 1개 블럭 failure까지 복구 가능
* 쓰기 동작이 sequential하고 많은 양을 쓸 경우 사용

#### Level 6
* parity 블럭 2개 사용
* 레벨5보다 더 좋은 data protection

### HW Issues
* Latent failure : 데이터가 손상되는 경우 (시간이 지나 자성이 떨어지는 등)
* Data Scrubbing : 주기적으로 failure 감지해 parity를 통해 복구하거나 다른 위치로 복사
* Hot Swapping : 시스템이 동작하는 동안 파워를 끄지 않은 채 디스크를 교체

<br/>

<br/>

<br/>

# Data Storage Structures
* DB = 파일(relation)의 모음
* 파일 = 레코드(tuple)의 집합
* 레코드 = 필드(attribute)의 집합

<br/>

## fixed length records
* 레코드를 배열처럼 고정 위치에 저장
* 레코드 삭제 시 shift
* 삭제 성능을 고려해 free list 유지
    * 빈 레코드 위치를 기록하는 리스트
    * 다음 빈 위치에 새 레코드를 쓰기

<br/>

## variable length records
* 파일 하나에 여러 레코드 타입 저장 (서로 다른 relation)
* varchar 등 가변 길이 속성 가지는 레코드 저장에 용이

### record structure
* 레코드의 각 필드에 접근 위함
* 고정 길이의 (offset, length) 필드
* 가변 길이의 속성 값 필드

### slotted page structure
* 페이지 내부에서 레코드의 위치를 찾기 위한 구조
* 헤더 : 레코드의 수, 다음에 삽입할 빈 공간의 위치, 각 레코드의 위치와 크기
* 맨 앞에 헤더 위치, 레코드는 맨 끝에서부터 추가

<br/>

## storing large objects
* blob/clob 타입 등 크기가 큰 객체
1. DBMS 외부 파일 시스템에 저장 이후 파일에 대한 포인터를 DB에 저장
2. 여러 조각으로 나누어 여러 relation의 튜플로 저장 : PostgreSQL TOAST
3. 클라우드 스토리지에 저장 후 주소를 DB에 저장

<br/>

## File Organization
* 레코드를 파일에 저장하는 위치, 구성을 정의
* B+트리와 해시를 이용한 기법은 Index 챕터 참고

### Heap
* 레코드를 파일의 빈 공간 어디든 배치 가능
* 위치가 할당된 후 보통 이동하지 않음
* 빈 위치를 효율적으로 찾는 것이 중요
* free space map : 블럭 단위로 비어 있는 공간의 비율을 나타냄
    * multi-level map 가능
    * sequential하게 fsm 탐색해 비율 큰 블럭에 삽입

### Sequential
* search key 순서대로 레코드 저장
* 순차적으로 접근하는 앱에서 사용
* 레코드 삭제 시 shift하는 대신 free space chain 활용
* 주기적으로 레코드를 순차적 순서로 재정렬

### Multitable Clustering
* 여러 테이블의 레코드를 같은 파일에 저장
* join 성능이 좋음
* 단일 테이블만 참조하는 쿼리의 성능이 안좋음
* 여러 테이블의 레코드를 저장하므로 가변 길이 레코드

<br/>

## Table Partitioning
* 크기가 큰 테이블을 나누어 여러 개의 테이블블로 저장
* 테이블을 수평(shard) 혹은 수직으로 분할
* 일부 동작은 더 빠르게 수행 가능
    * 한 쪽 테이블만 참조하는 경우
    * 여러 분할한 테이블을 동시에 참조해 작업 가능한 경우

<br/>

## Buffer
* DBMS의 메모리 공간의 일부를 버퍼로 할당
* 버퍼에 디스크 블럭을 가져와 캐시처럼 활용
### Buffer Manager
* 버퍼에서 블럭의 위치를 찾기 위함
* 버퍼에 블럭이 있다면 블럭의 메모리 주소 반환
* 없다면 디스크에 접근해 블럭을 버퍼에 저장
* 버퍼가 꽉 찼다면 하나를 선택해 디스크에 저장

### Lock
* 블럭에 대한 동시 접근을 관리
* 읽기 동작 수행 전 shared lock 획득
* 쓰기 동작 수행 전 exclusive lock 획득

### Replacement Policy
* LRU : 가장 최근에 사용하지 않은 블럭을 대체
* Toss-immediate : 블럭의 마지막 튜플까지 사용한 뒤 바로 대체
* MRU : 가장 최근에 사용한 블럭을 대체
* 여러 쿼리가 블럭을 접근하는 패턴에 따라 각 전략의 성능이 달라짐
    * 예를 들어 nested loop join은 LRU 성능이 좋지 않음
    * 데이터에 접근하는 패턴을 보고 적절한 전략을 고르자
    * data dictionary는 자주 접근되므로 버퍼에 유지하는 등 휴리스틱

### Optimizing Block Access
* non volatile write buffer
    * 대체되는 블럭을 디스크 대신 버퍼에 저장
    * 시스템 idle 시 버퍼의 내용을 디스크로 옮김
* log disk
    * sequential하게 블럭 업데이트의 로그를 작성
    * seek 필요 없으므로 디스크보다 빠름
* journaling file systems
    * nv-ram이나 log disk 활용
* columnnar representation
    * relation의 각 속성을 다른 테이블에 저장
    * 일부 속성만 접근할 경우 빠른 속도, 캐시 지역성
    * 일반적인 표현을 열 단위 표현으로 재구성하는 비용 비쌈
    * 튜플 삭제 및 갱신이 비쌈
