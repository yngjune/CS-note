# E-R 모델을 Relation Schema로 변환
* 객체 집합과 관계 집합을 모두 relation으로 표현

## Entity Set 변환
### simple attribute
* 동일한 속성을 가지는 schema로 변환

### composite attributes
* 계층이 존재하는 속성을 flatten
* 상위 속성의 이름을 접두사로 붙임
* ambiguity가 없다면 접두사 생략 가능

### derieved attributes
1. 속성을 추가하지 않음
    * 호출 시 사용자 앱에서 구현
    * 사용자 프로세스가 감당
2. schema에 속성을 추가
    * 정보의 중복이 다소 존재
    * 다른 열을 기반으로 계산하므로 주기적인 갱신 필요

### multivalued attributes
1. 값의 개수가 적은 경우 schema의 속성으로 정의
    * 예를 들어, 전화번호가 개인번호, 사무실 번호 2개로 제한 시
    * 개인 번호, 사무실 번호 2개의 속성을 스키마에 추가
2. 새로운 관계를 정의
    * 기존 관계의 PK와 속성값 2개의 속성을 가지는 관계
    * phones = (emp-no, phone)
    * 두 속성을 묶어서 PK로 정의

<br/>

## Relationship Set 변환
### many-to-many
* 두 스키마의 PK를 속성으로 가지는 스키마로 정의

### one-to-many
* many 쪽의 schema에 one의 PK 속성을 추가
* 만약 many 쪽이 partial이라면 새로 추가한 속성이 null 값을 가질 수 있음

### one-to-one
* 양 쪽 모두 many로 선택 가능
* one-to-many와 같은 방식으로 변환
* 단, 비즈니스 로직을 고려해 선택

### weak entity set
* weak 스키마에 strong 스키마의 PK 속성을 추가
* 추가한 strong의 PK와 weak의 discriminator 묶어 PK로 사용

<br/>

## Specialization 표현
1. local 속성만 가지는 하위 개체
    * 하위 개체는 상위 개체의 PK와 하위 개체의 local 속성을 포함
    * 하위 개체의 속성 접근을 위해 여러 개의 개체에 접근이 필요
2. 모든 속성 상속
    * 하위 개체는 상위 개체의 PK와 모든 속성, 하위 개체의 local 속성을 포함
    * 중복되는 속성이 여러 번 정의됨