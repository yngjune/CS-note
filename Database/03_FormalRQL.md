# Formal Relational Query Language

## Relational Algebra
* procedural query language
* 1개 혹은 2개의 relation을 입력받고 1개의 relation을 출력하는 동작으로 구성
<!-- * `relational algebra expression`
    * 각 동작이 같은 형식(1개의 관계)을 출력
    * 여러 동작을 결합하기 용이
    * expression의 결과를 다음 expression에 사용 -->

<br/>

### fundamental operations
#### select
* $\sigma_{predicate}(r)$
* relation에서 predicate을 만족하는 튜플을 선택
* $\sigma_{dept_name="Physics"}(instructor)$
* SQL의 where 문과 같은 역할 수행

#### project
* 선택한 attribute만 남긴 relation을 반환
* $\Pi_{A_1,A_2,...,A_k}(r)$
* $\Pi_{ID, name}(instructor)$
* 중복된 튜플은 결과에서 제거됨 (relation은 집합이므로)

#### union
* $r \cup s$
* 두 relation이 다음 조건을 만족
    * same arity : attribute의 수 같음
    * domain compatible : 같은 위치의 열이 같은 타입의 값

#### set difference
* $r - s$
* 두 relation이 다음 조건을 만족
    * same arity : attribute의 수 같음
    * domain compatible : 같은 위치의 열이 같은 타입의 값

#### cartesian product
* $r \times s = \{tq | t \in r \ and \ q \in s \}$
* 두 relation의 도메인이 disjoint해야 함
* 교집합이 존재할 경우 rename

#### rename
* $\rho_x(E)$
* $\rho_{x(A_1,A_2,...,A_k)}(E)$
* expression E를 x로 이름을 바꾼 결과 반환

<br/>

### Additional Operations
#### set intersection
* $r \cap s = \{ t\ |\ t \in r \ and \ t \in s \}$
* 두 relation이 다음 조건을 만족
    * same arity : attribute의 수 같음
    * domain compatible : 같은 위치의 열이 같은 타입의 값

#### natural join
* $ r \Join s$
* 두 relation r, s에서 튜플 $t_r$과  $t_s$를 선택
* 공통된 열 $R \cap S$이 같은 값을 가지는 경우 $t_r$과 $t_s$를 결합한 튜플을 결과에 추가
* `associative` : $ (a \Join b) \Join c = a \Join (b \Join c) $
* `commutative` : $ r \Join s = s \Join r$

#### theta join
* $r \Join_{\theta} s = \sigma_{\theta}(r \times s)$
* 주어진 조건으로 두 관계를 join

#### assignment
* 복잡한 쿼리를 나타내기 위해 사용
* 프로그래밍 언어의 변수 개념
* assignment의 결과를 다음 expression에서 사용
* 예시 : $temp1 \leftarrow \Pi_{R-S}(r)$

#### outer join
* 정보의 손실을 방지하기 위함
* left outer join
    * $R \cap S$ 열의 값이 같은 $t_s$가 없는  $t_r$에 대해
    * $R - S$를 null로 채워 결과에 추가
* right outer join
    * $R \cap S$ 열의 값이 같은 $t_r$이 없는  $t_s$에 대해
    * $S - R$을 null로 채워 결과에 추가
* full outer join : 양 쪽 방향을 수행

<br/>

### Extended Operations
#### Generalized Projection
* $\Pi_{F_1,F_2,...,F_n}(E)$
* 기존의 projection은 attribute를 이용
* 대신에 attribute 및 상수를 이용한 수식을 사용한 projection

#### Aggregate Functions
* `aggregate function` : 하나의 값을 리턴
    |function|desc|
    |--|--|
    |avg|평균|
    |min|최솟값|
    |max|최댓값|
    |sum|값의 합|
    |count|값의 개수|
    |count-distinct|서로 다른 값의 개수|
* $_{G_1,G_2,...,G_n}G_{F1(A_1,...,A_k),...,F_n(...)}(E)$
* $G_i$ : 수식을 계산할 그룹 (list of attributes)
* 각 그룹에 대해 $F_i$를 계산

<br/>

### Relational Algebra Expression
* 위에서 기술한 동작들은 모두 동일하게 하나의 relation을 반환
* `expression`을 통해 관계대수를 formal하게 정의
* Basic expression
    * constant relation
    * DB에 존재하는 relation
* expression을 이용해 동작을 수행한 결과 또한 expression

<br/>

## Tuple Relational Calculus
* non-procedural : 과정 없이 얻고자 하는 정보만 기술
* $\{ t \ | \ P(t) \}$
* 조건 P를 만족하는 모든 튜플 t를 반환
* 예시 : $\{ t \ | \ t \in instructor \wedge t[salary] \gt 80000\}$

<br/>

## Domain Relational Calculus
* non-procedural : 과정 없이 얻고자 하는 정보만 기술
* 도메인을 변수로 사용
* $\{ <x_1, ..., x_n> \ | \ P(x_1,...,x_n) \}$