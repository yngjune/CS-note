# Query Optimization
* 주어진 쿼리와 같은 결과를 생성하는 여러 expression tree 존재
* 각 동작(select, join 등) 수행하는 여러 알고리즘 존재
* `evaluation plan` : 성능을 고려해 결정한 최종 실행 트리 및 알고리즘
    * equivalence rule 통해 여러 수식 트리 생성
    * 테이블의 통계 정보 (튜플의 수, 블럭의 수, 속성의 수) 등 사용
    * 대부분의 DB 쿼리 언어에서 선택된 플랜을 표시할 수 있음 (eg. `explain analyse` in postgreSQL)

<br/>

## Equivalent Expression 생성
* `equivalent` : 같은 튜플의 집합을 생성하는 두 관계 대수는 equivalent

### Equivalent Rules
* conjunctive selection => sequence of selection
* commutative selection
* commutative natural(+theta) join
* associative natural join
* pushing selection, pushing projection
* 등등 많은 equivalence rule 존재

### Pushing Selection
* selection 동작을 join 이전에 미리 수행해 join의 크기를 줄임
* $\sigma(a \ join \ b) = \sigma(a) \ join \ b$

### Pushing Projection
* projection 동작을 join 이전에 미리 수행해 join의 크기를 줄임
* 다만 projection이 조인 키에 영향을 주지 않는지 유의

### Join Ordering
* 연속으로 여러 개의 Join을 수행 시 어떤 것을 먼저 수행?
* `selectivity` 가 좋은 것을 먼저 수행해 join하는 튜플의 양을 줄임
    * 동작 수행 결과 튜플의 수가 적으면 좋은 selectivity 가짐

<br>

## Practical Plan Selection
* 모든 가능한 플랜을 조사해 최적의 비용을 가지는 플랜을 선택하는 것은 비용이 비쌈
* 대신 여러 휴리스틱을 섞어 플랜을 선택

### interaction of each operations
* 각 동작에 대해 따로 최적의 알고리즘을 선택하는 것이 최적의 플랜이 아닐 수 있음
* merge join은 hash join에 비해 비용이 크지만 결과 튜플이 정렬되어 있어 다음 동작의 비용이 낮아짐
* nested loop join은 비용이 크지만 pipelining 가능

### DP in Optimization
* join ordering의 예시에서, n개의 연속된 join을 수행하는 순서는 매우 많음
* 대신 k개의 relation에 대한 최적의 join 순서를 DP로 저장해 반복적으로 계산하지 않음

### Left Deep Join Tree
* join 순서 트리를 왼쪽으로만 확장, 즉 오른쪽 자식은 join 결과가 아닌 relation
* push (selection/projection) 이 쉬움
* pipelining sync 맞추기 쉬움
* 모든 left deep join tree를 조사하는 데 $O(N*2^N)$ 으로, 모든 경우에 비해 싸다
* join의 수가 많지 않고 데이터셋의 크기가 크다면 모든 플랜을 조사할 가치가 있음

### Heuristics
* pushing selection / projection
* selectivity가 높은 select, project를 먼저 수행
* left deep join tree만 고려
* plan caching을 통해 비슷한 플랜의 최적화 결과를 사용
* nested query 등 복잡한 쿼리는 최적화가 복잡함


<br/>

## Statistical Informations
* 튜플의 수, 튜플의 크기, 블럭의 수 등
* 통계 정보를 이용해 select, join 등 연산의 비용을 계산
* $f_r$ (blocking factor) : 블럭 하나 당 튜플의 수 등
* $V(A, r)$ : 속성 A의 서로 다른 값의 수
* 통계 정보를 주기적으로 업데이트 필요

### Selection Size
* $\sigma_{A=v}(r)$의 크기 : $n_r / V(A,r)$
* $\sigma_{A \ge v}(r)$의 크기 : $n_r * \frac{v-min(A,r)}{max(A,r)-min(A,r)}$
* 히스토그램 통계 정보가 있다면 범위 질의의 크기를 더 정확하게 예측 가능

### Join Size
* $R \cap S$가 공집합일 경우 cartesian product로 조인의 크기는 $n_rn_s$개
* $R \cap S$가 r의 키일 경우, s의 각 튜플에 대해 최대 1개 튜플이 일치하므로 조인 크기는 최대 s개
* 키가 아닐 경우 $n_rn_s / max(V(A,s),V(A,r))$

<br/>

## Materialized View
* 자주 호출되는 중간 결과를 view로 정의한 뒤 결과를 스토리지에 저장
* view의 consistency 유지를 위해 업데이트 필요
    * 트리거를 정의
    * 주기적으로, 혹은 시스템이 idle할 때 다시 계산
    * incremental maintainance