# Entity-Relationship Model

## DB Development Life Cycle
1. 데이터 및 사용자 요구사항 분석
    * data cleaning : 중복되거나 비어있는 데이터 처리
    * 비즈니스 요구사항을 고려
2. Conceptual Data Design
    * ER 모델링
    * 데이터 모델 검증
    * 분산 DB 설계
3. Logical/Physical DB Design
    * logical design : 설계한 ER 모델을 relational schema로 변환
    * normalization, refinement : 좋은 relation을 가지도록 정제
4. DB implementation
    * 테이블 생성
    * 데이터 적재
    * test / fine-tune

<br/>

## E-R Model
* 데이터베이스를 `entity`와 `relationship`으로 정의
* 객체를 사각형, 관계를 마름모로 나타냄

### Entity Set
* `entity` : 다른 객체와 구분 가능한 실존하는 객체
    * 요구사항의 명사 단어들
    * 물리적으로/개념적으로 존재하는 개념적인 객체
* `attribute` : 개체가 가지는 특성
* `entity set` : 같은 타입 개체들의 집합

### Weak Entity Set
* `weak entity set` : primary key가 없는 객체 집합
* identifying `strong entity set`의 존재에 종속됨
    * total, one-to-many relationship set으로 관계 표현
    * total : 약한 개체 집합의 모든 개체가 관계에 참여. 강한 개체는 0개 이상 참여
    * one-to-many : 강한 개체 하나가 약한 개체 한 개 이상과 관계
    * 이중선 마름모로 관계 표현
    * 예시 : 사원(강한) - 자녀(약한)
    * on delete cascade
* `discriminator` : 약한 객체 집합의 객체들을 구분하는 attribute들의 집합
    * 점선 밑줄로 표현
* 의존하는 강한 객체 집합의 PK + discriminator로 약한 객체 집합의 PK 표현


### Attribute
* 객체를 속성의 집합으로 표현
* `domain` : 각 속성이 가질 수 있는 값의 범위(집합)
* `composite attr` : 여러 속성으로 이루어진 속성
* `multivalued attr` : 여러 개의 값을 가지는 속성
* `derived attr` : 다른 속성의 값을 이용해 계산되는 속성

#### Redundant attribute
* 관계를 통해 두 개체 집합을 연결할 때, 개체의 속성이 중복될 수 있음
* 예를 들어 교수 개체 집합과 학과 개체 집합 관계의 경우
    * 학과 개체의 학과명 속성
    * 교수 개체의 학과명 속성 중복
* 중복되는 속성이더라도 ER 모델을 테이블로 변환할 때 다시 도입되는 경우도 존재함

### Relationship Set
* 2개 이상의 개체 간의 관계를 나타냄
* `relationship set` : 같은 타입의 관계의 집합
* 관계도 `attribute` 가질 수 있음

#### Degree of Relationship Set
* ER모델의 대부분의 관계는 binary (2개의 개체)
* unary : 하나의 개체 집합 간 관계
    * 강의와 강의의 선수과목 관계
    * 직원 간 매니저/상사 관계 등
* N-ary : N개의 객체 집합으로 구성된 관계

#### Cardinality Constraints
* one to one
* one to many
* many to many

#### Participation Constraints
* total : 개체 집합의 모든 개체가 관계에 참여
* partial : 일부 개체가 관계에 참여하지 않아도 됨

#### Keys for Relationship Sets
* 참여하는 개체 집합의 PK를 조합해 관계 집합의 superkey
* 후보 키 선정 시 mapping cardinality 고려
* PK 선정시 관계 집합의 의미를 고려

#### N-ary to Binary
* 어떤 관계는 있는 그대로 구현 시 n-ary 관계가 됨
* n-ary를 binary 관계로 변환하는 것이 좋은 경우가 존재
* 개체 집합을 추가로 구성해 n-ary를 binary 관계로 변환 가능

<br/>

## Extended E-R
### Generalization / Specialization
* bottop-up 디자인
* 상위 개체 집합이 하위 개체 집합의 속성을 공유함
* superclass - subclass의 개념
* 상위 개체 쪽으로 갈수록 generalize
* 하위 개체 쪽으로 갈수록 specialize

#### Constraints
* 사용자가 정의한 조건을 기반으로 ISA 관계 정의 가능
* total vs partial : 개체가 하위 개체 집합에 포함되어야 하는지
* disjoint vs overlapping : 개체가 여러 하위 개체 집합에 중복 포함될 수 있는지