# Introduction

## 파일 시스템을 통한 데이터 관리의 단점
1. redundancy and inconsistency
    * 같은 데이터가 서로 다른 위치의 파일에 중복으로 저장될 경우
    * 한 위치에서 수정, 삭제 등 수행 시 위치 간 정보가 서로 다른 상태가 됨
2. difficulty in accessing data
3. data isolation
4. integrity problems
5. atomicity of updates
6. concurrent access by multiple users
7. data dependence problem
    * 파일에 작성된 데이터에 의존하는 프로그램 작성
    * 파일 형식이 수정되면 코드 또한 수정해야 함

<br/>

## View of Data
* physical level : 데이터가 실제로 디스크에 어떻게 저장되어 있는지
* logical level : 데이터베이스에서 데이터의 구조, 관계, 타입 등
* view level : 데이터베이스의 일부분 (필요로 하는 데이터만)

<br/>

## Instance vs Schema
* schema : 데이터베이스의 논리적인 구조
    * physical schema : physical level에서의 DB 디자인
    * logical schema : logical level에서의 DB 디자인
* instance : 실제 데이터
* physical data independence : 프로그램은 데이터의 논리 구조에 의존함. 실제 데이터의 물리적 구조와는 무관함.

<br/>

## Data Model
데이터의 성질(구조), 데이터 간의 관계, 데이터의 semantics, consistency constraints 등을 설명하는 모델

* Relational Model
* Entity-Relationship(ER) Model

<br/>

## Database Languages
### Data Definition Language (DDL)
* data dictionary에 저장됨
* 데이터에 대한 메타데이터를 기술함
    * DB schema
    * integrity constraints (primary key, referential integrity 등)
    * authorization

### Data Manipulation Language (DML)
* SQL 등의 query language
* insert, delete, update, retrieval 등 데이터와 관련된 조작
* 명령형(procedural) 언어 : 어떤 데이터를 어떻게 가져올지 기술 (how)
* 선언형(declarative) 언어 : 어떤 데이터를 필요로 하는지 기술 (what) - SQL 등

### Data Control Language (DCL)
* 데이터에 대한 접근을 제어
* 여러 사용자의 동시 접근 등


<br/>

## Query Processing Procedure
1. 사용자가 SQL 등을 이용해 query
2. relational algebra expression으로 번역
3. 최적화
4. 최적화된 relational algebra expression 실행
5. 결과를 사용자에게 반환