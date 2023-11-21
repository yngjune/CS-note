# Query Processing
## 쿼리 처리 단계
### Parsing & Translation
* SQL 등 쿼리 언어를 관계 대수로 번역
* 파서를 통해 쿼리의 문법 및 relation 검증

### Optimization
* 같은 결과를 반환하는 관계 대수 expression tree가 여러 가지 존재
* join 등 각각 동작을 구현하는 여러 가지 알고리즘이 존재
* DB catalog의 통계 정보를 활용해 비용을 계산
    * 관계의 튜플의 수, 튜플의 크기 등
    * 비용이 가장 낮은 evaluation plan 결정

### Evaluation
* 최적화 결과 얻은 query plan 실행
* 쿼리 결과를 사용자에게 반환

<br/>

## Query Cost
* total resource consumption을 성능의 지표로 활용
* 디스크 접근, CPU cost, 네트워크 접근 (분산DB 사용 시)

### Disk Cost
* seek time $t_s$
* block transfer(r/w) $t_T$ : 단순성을 위해 읽기와 쓰기의 비용 같다고 가정
* 총 $b \times t_T + S \times t_s$ 의 비용 발생
* 사용하는 스토리지 종류에 따라 성능 편차
* 접근하는 블럭이 버퍼에 있는 경우를 제외하고, 모두 디스크에 접근해야 하는 최악의 경우 가정

<br/>

## Selection
### linear scan
* 테이블을 처음부터 끝까지 각 블럭을 가져와 검사
* cost = $b_r * t_T + 1 * t_s$ (seek 1회, 블럭 개수만큼  transfer)
* 키를 기준으로 탐색하는 경우 레코드 발견 시 탐색 중단
    * 키 값을 가지는 레코드는 unique 하므로
    * 평균 비용이 $(b_r / 2) * t_T + 1 * t_s$로 감소
* 인덱스 유무, 레코드 정렬 여부와 관계없이 적용 가능

### clustering index, search on key
* 인덱스를 통해 레코드 위치 찾은 후 레코드를 버퍼로 가져옴
* cost = $(h_i + 1) * (t_T + t_s)$
* B+ 트리 인덱스 높이만큼 seek + transfer 이후 데이터 레코드를 seek + transfer

### clustering index, search on nonkey
* 인덱스를 통해 레코드 위치 찾은 후 레코드(한 개 이상)를 버퍼로 가져옴
* cost = $h_i* (t_T + t_s) + t_s + b * t_T$
* B+ 트리 인덱스 높이만큼 seek + transfer 이후 b개의 데이터 블럭을 순차적으로 transfer

### secondary index, search on key/nonkey
* 키에 대한 탐색일 경우, 마찬가지로 cost = $(h_i + 1) * (t_T + t_s)$
* 키가 아닌 속성에 대한 탐색일 경우, cost = $(h_i + n) * (t_T + t_s)$
    * n개의 데이터 레코드가 각각 서로 다른 데이터 블록에 위치할 수 있음
    * expensive!

<br/>

## Advanced Selection
* `comparison` : $\sigma_{A \le V}(r)$ 혹은 $\sigma_{A \ge V}(r)$ 조건을 포함하는 선택
    * 인덱스를 통해 탐색하거나 파일을 linear scan
* `conjunction` : 여러 조건을 동시에 만족하는 튜플 검색
* `disjunction` : 여러 조건 중 하나 이상을 만족하는 튜플 검색
* `negation` : 조건의 부정을 만족하는 튜플 검색

### clustering index, comparison
* $\sigma_{A \ge V}(r)$ 의 경우
    * 인덱스를 통해 V보다 크거나 같은 첫 튜플 위치 탐색
    * 이후 파일을 linear scan
* $\sigma_{A \le V}(r)$ 의 경우
    * 파일의 처음부터 V보다 작거나 같을 때까지 linear scan
    * 굳이 인덱스를 탐색하지 않아도 됨

### secondary index, comparison
* 인덱스를 활용하지 않고 파일을 linear scan
    * 인덱스를 기준으로 레코드가 정렬되지 않음
    * 두 경우 모두 디스크에 대한 랜덤 위치 접근이 발생할 수 있음

### negation
* 기본적으로 파일을 linear scan
* 인덱스는 조건을 "만족"하는 튜플 위치를 찾기 때문에 "만족하지 않는" 튜플을 찾기 적합하지 않음

### disjunctive selection
* 만약 모든 조건에 대해 인덱스를 가지고 효율적으로 검색 가능하다면
    * 각 조건을 인덱스를 이용해 탐색 이후 결과를 합집합
* 아니면 파일을 linear scan

### conjunctive selection
1. 모든 조건을 각 조건에 적합한 방법으로 탐색 후 결과를 교집합
2. 하나의 인덱스를 골라 그 인덱스로 탐색 후 나머지 조건을 메모리에서 검사
    * selectivity가 좋은 (가장 적은 튜플을 선택하는) 인덱스를 선택
    * 하나의 조건을 인덱스로 탐색 후 나머지 조건을 메모리 버퍼에서 검사

<br/>

## External Sorting
* 메모리보다 크기가 큰 데이터를 정렬하는 방법
* 메모리가 M개 블럭 크기인 경우

### create runs
```python
repeat:
    read M blocks into memory
    sort blocks
    write sorted data to run[0:N]
until end of r
```

### merge the runs (n-way merge)
```python
block[0:N] = buffer to input runs
block[N] = buffer to output

repeat:
    tmp = []
    for i in range(N):
        tmp.append(popFirstRecord(block[i]))
    tmp.sort()
    block[N].append(tmp)
    
    if block[N] full:
        output block[N] to disk
    
    for i in range(N):
        if block[i] empty:
            block[i] = getNextBlock(i)
until input buffer pages empty
```
1. 메모리의 크기가 M개 블럭이라고 가정
2. 각각을 메모리의 크기(M)만큼 정렬한 결과 N개의 런 생성
3. 각 런을 병합
    * 만약 N이 M보다 크거나 같다면 한 번에 병합이 불가능함
    * 그럴 경우 런의 개수가 1개가 될때까지 여러 번 병합 알고리즘 수행

<br/>

## Join
### Nested Loop Join
```python
NestedLoopJoin(r, s):
    for t_r in r:
        for t_s in s:
            if satisfy_condition(t_r, t_s):
                result.append(t_r.concat(t_s))

    return result
```
* 모든 튜플의 쌍에 대해서 검사하기 때문에 비용이 비쌈
* 더 작은 크기의 relation을 inner relation으로 사용하는 것이 좋음
* 메모리가 부족하다면 최악의 경우 $n_r + b_r$ seek, $n_r * b_s + b_r$ transfer
* inner relation의 크기가 메모리에 들어온다면 $b_r + b_s$ transfer, 2 seek

### Block Nested Loop Join
* 튜플이 아닌 블럭 단위로 nested loop join
* worst case : $b_r * b_s + b_r$ transfer, $2 * b_r$ seek
* best case : $b_r + b_s$ transfer, 2 seek
    * inner relation이 메모리에 다 들어오는 경우
* join이 안쪽 relation의 키인 경우 튜플 발견 시 중단 가능
* 메모리의 크기가 충분한 경우 r의 블럭을 한 번에 여러개 읽어와 성능 향상
    * transfer 횟수에서 $b_r * b_s$ 텀을 감소시킴

### Indexed Nested Loop Join
* r의 각 튜플의 값에 대해 s의 인덱스를 탐색해 조인
* inner relation 탐색을 index 탐색으로 대체할 수 있는 경우
    * join 조건이 equi join 이거나 natural join인 경우
    * join 속성에 대해 inner relation의 인덱스가 존재하는 경우
* 최악의 경우 성능 $b_r(t_T + t_s) + n_r * c$
    * 버퍼에서 r의 블럭 하나에 대한 공간만 존재할 경우
    * c는 s의 인덱스를 탐색하고 튜플을 가져오는 seek, transfer 시간
    * selection cost로 c의 비용을 예측

### Merge Join
1. join 키를 기준으로 양쪽 relation을 정렬
2. merge하며 join
* equi-join과 natural join에 대해 사용 가능
* cost = $b_r + b_s$ transfer, $b_r/b_b + b_s/b_b$ seek
    * 한 번에 r, s로부터 여러 개의 페이지를 읽어와 merge

#### hybrid merge join
1. 한 쪽이 정렬되어 있고, inner relation은 secondary B+Tree 인덱스 있는 경우
2. 정렬된 relation의 튜플을 인덱스 엔트리와 merge (레코드에 대한 주소 가지게)
3. 레코드의 주소를 기준으로 정렬
4. 주소 순서대로 레코드의 위치를 방문해 주소를 실제 레코드로 대체

### Hash Join
1. 양쪽 relation을 조인 키를 이용하는 해시함수 h를 기준으로 파티션
    * 혹은 한쪽 relation만 해시 함수를 이용해 파티션
2. 해시 값이 같은 파티션끼리 join 수행
* equi-join과 natural join에 대해 사용 가능

<br/>

## 기타 Operations
### Duplicate Elimination
* 튜플을 정렬 시 인접하게 되는 중복 요소를 제거
* 외부 정렬의 merge 단계를 변형해 중복 제거 가능
* 해싱을 수행하면 중복 요소는 같은 버킷으로 들어감

### Set Operations
* 합집합, 교집합, 차집합 등
* 정렬 이후 merge join의 변형
* 해시 조인의 변형

### Outer Join
* 조인 이후 조인에 참여하지 않은 튜플에 null 추가

### group by
* 그룹 속성에 대한 정렬이나 해싱을 이용해 같은 그룹을 같은 위치에 배치

<br/>

## evaluating entire expression tree
### Materialized evaluation
* expression tree의 맨 밑에서부터 시작
* 각 동작(select, join 등)을 평가 후 중간 결과를 디스크에 저장
* 다음 동작은 중간 결과를 다시 디스크에서 읽어 와 수행
* 모든 경우에 대해서 적용 가능한 평가 방식. 하지만 매번 디스크에 읽고 쓰므로 비용이 큼

### Pipelined evaluation
* 이전 동작의 결과 튜플을 다음 동작으로 바로 전달
* 디스크에 쓰지 않으므로 효율적이지만, 모든 동작에 대해 적용 가능하지 않음
    * 정렬, 해시 조인 등은 끝날 때까지 모든 튜플이 필요
* demand-driven (lazy) 구현
    * 상위 동작이 하위 동작에게 다음 튜플을 요청
    * 동작 사이에 state를 유지해 다음으로 전달할 튜플 인지
* producer-driven (eager) 구현
    * 하위 동작이 튜플 생성 시 상위 동작에게 넘겨줌
    * 각 동작 사이에 버퍼를 유지. 하위 동작이 버퍼에 튜플을 넣고 상위 동작이 빼내 사용