# 데이터베이스 정리

## Relational Model
- 테이블 : relation
- 행 : tuple
- 열 : attribute
- 도메인(domain) : 각 열의 도메인은 해당 열에 허용된 값들의 집합
  - 모든 열의 도메인은 atomic -> 나눌 수 없는 값

#### Keys
- superkey(슈퍼 키)는 해당 값이 유일한 튜플을 식별할 수 있는 것\
- candidate key(후보 키)는 superkey들 중 값이 가장 적은 것 ex {ID} {ID, name} superkey들 중 {ID}가  candidate key
- candidate key들 중 하나를 선택하여 primary key(기본 키)가 될 수 있다.
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

#### 정규화
- 관계형 데이터베이스의 설계에서 중복을 최소화하게 데이터를 구조화하는 프로세스

#### 이상 현상 (Anomaly)
- 테이블 수정 시 생기는 부작용
- 갱신 이상 : 같아야 하는 정보가 갱신 후 여러 행에 걸쳐서 다른 정보로 표현되었을 때
- 삽입 이상 : null이 포함될 수 없는 테이블에서 하나의 컬럼 부재 때문에 삽입을 못할 때
- 삭제 이상 : 정보 삭제 시, 삭제되면 안되는 정보까지 함께 삭제될 때

#### 제1정규형
- 한 컬럼에 두 가지 이상의 정보가 들어가지 않아야 함
- 원자성(atomicity) : 나누어지지 않는 상태

#### 제2정규형
- 제1정규형 만족
- 후보키 K와 K에 속하지 않는 컬럼 A가 있을 때, A를 결정하기 위해 K 전체를 참조해야만 하는 경우

#### 제3정규형
- 제 2정규형을 만족
- 키가 아닌 모든 컬럼이 모든 키에 이행적 종속이 되지 않아야함
- 이행적 함수 종속 : X -> Y 이고 Y -> Z 일 때 X -> Z 가 되는 것

#### BCNF 정규화
- 제 3정규형을 만족
- 모든 결정자가 후보키인 경우


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
