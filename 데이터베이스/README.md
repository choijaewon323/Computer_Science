# 데이터베이스 정리

## Relational Model
- 테이블 : relation
- 행 : tuple
- 열 : attribute
- 도메인(domain) : 각 열의 도메인은 해당 열에 허용된 값들의 집합
  - 모든 열의 도메인은 atomic -> 나눌 수 없는 값

#### Keys
- superkey는 해당 값이 유일한 튜플을 식별할 수 있는 것\
- candidate key는 superkey들 중 값이 가장 적은 것 ex {ID} {ID, name} superkey들 중 {ID}가  candidate key
- candidate key들 중 하나를 선택하여 primary key가 될 수 있다.
- foreign key(외래 키) 제약 : 한 테이블의 값이 반드시 다른 테이블에서 나타나야 한다.

#### relational algebra
- Select: `𝜎` - 튜플(행) 선택
- Project: `Π` - 속성(열) 선택
- Union: `∪` - 합집합
- Set difference: `–` - 차집합
- Cartesian product: `×` - 테이블 조인 전체 곱을 나타냄
- Rename: `ρ`

- 내추럴 조인 : `⋈` - 두 릴레이션 R, S에 대해서 교집합인 원소들의 집합
- 세타 조인 : `⋈θ` - `R ⋈θ S = σθ(R × S)` 과 동일


## SQL Basics

#### create
create table r(a1 d1, a2 d2,
	(integrity-constraint)
)

#### integrity constraint
- primary key
- foreign key() references r
- not null

#### alter
- alter table r add A D -> 테이블 r 에 D 도메인인 A 어트리뷰트 추가
- alter table r drop A -> 테이블 r에 A 어트리뷰트 삭제

- 전형적인 SQL 쿼리
```
select A1, ... An
from r1, r2, ..., rm
where p
```
-> SQL 쿼리의 결과는 릴레이션

#### select 절
- 속성들을 뽑아냄
- 디폴트로 중복을 허용, 중복을 없애고 싶으면 distinct

#### where절
- 조건 명시

#### from 절
- 카티시안 프로덕트와 동일하다.

#### order by
- 순서를 지정할 수 있음
- 오름차순이 디폴트

#### group by
- 특정 속성에 대한 그룹으로 연산 수행

#### 중첩된 서브 쿼리
```
select distinct course_id
from section
where semester = ’Fall’ and year = 2009 and course_id in
  (select course_id
  from section
  where semester = ’Spring’ and year= 2010);
```


## Intermediate SQL

#### inner join
- 정확히 매치하는 튜플들만 포함시키는것

#### outer join
- 매치되지 않는 튜플도 포함시키는 것
- 매치되지 않는 속성은 null
- left outer join : 왼쪽 릴레이션의 튜플들은 모두 포함
- right outer join : 오른쪽 릴레이션의 튜플들 모두 포함
- full outer join : 왼쪽, 오른쪽 모두 포함

#### transaction
- 여러 SQL이 하나의 동작으로 이루어지는 것
- 전체 다 실행되거나(commit), 모두 실행되지 않아야 함(roll back)




## ER model
- entity : 다른 모든 객체들과 구별되는 사물
- entity들은 속성을 가진다 ex) 사람은 이름과 주소가 있다.
- entity set : 같은 속성을 공유하는 엔티티들의 집합
- domain : 각 속성들에 허락된 값들의 집합

#### 속성의 분류(유형)
- 단순 속성(simple attribute) - 복합 속성(composite attribute)
- 단일값 속성(single-valued attribute) - 다중값 속성(multivalued attribute)
- 유도 속성(derived attribute) - 저장 속성

#### 대응 카디널리티(mapping cardinality)
- 일대일(one to one)
- 일대다(one to many)
- 다대일(many to one)
- 다대다(many to many)

#### Keys
- super key : 엔티티 하나만을 식별할 수 있는 속성들의 집합
- candidate key : super key들 중 속성의 수가 가장 적은 키
- primary key : candidate key 중 선택된 키

- primary key의 조합으로 relationship의 super key를 만들 수 있다.
-> 엔티티 셋 둘의 조합에는 최대 한개의 relationship이 존재

## Relational DB design

#### First Normal Form (제 1 정규형)
- 모든 속성의 도메인이 atomic 한 경우

#### BCNF (Boyce-Codd 정규형)
- 모든 함수적 종속성 a -> b에 대해서 a가 R의 수퍼키인 것

#### Third Normal Form (제 3 정규형)
- 모든 함수적 종속성 a -> b에 대해서 a가 R의 수퍼키이거나, b - a에 속한 각 속성이 R의 후보키일 때


## Indexing

#### Basic Concepts
- 인덱싱은 원하는 데이터에 빠르게 접근하기 위해 존재
- 인덱스 파일은 레코드(인덱스 엔트리)들로 이루어짐

- 인덱스 엔트리

|search-key|pointer|
|---------|-----------|

- 인덱스 종류
	- Ordered indices : 검색 키가 정렬되어 저장
	- Hash indices : 검색 키가 hash function을 이용한 bucket을 통해 분포되어 있음

#### Ordered Indices
- Primary index : 검색 키가 파일의 순서와 일치하는 인덱스 (clustering index)
- Secondary index : 검색 키가 파일의 순서와 일치하지 않는 인덱스 (non-clustering index)
