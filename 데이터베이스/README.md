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

## 트랜잭션 (Transaction)
- 하나의 논리적 기능을 수행하기 위한 작업 단위
- 여러 쿼리를 하나로 묶는 단위

### ACID – 트랜잭션의 특징

#### 원자성 (Atomicity)
- 모두 수행되거나 아예 수행되지 않거나를 보장
- 커밋 (commit) : 여러 쿼리가 성공적으로 처리됨을 확정하는 명령
- 롤백 (rollback) : 하나의 묶음 과정 이전으로 돌리는 명령

#### 일관성 (Consistency)
- 허용된 방식으로만 데이터 변경 가능하게
- ex) 0원인 사람이 500만원 인출 불가

#### 격리성 (Isolation)
- 트랜잭션 수행 시 서로 끼어들지 못하게
- 격리 수준 : 위로 갈수록 격리성 높음, 아래로 갈수록 동시성 높음
	- SERIALIZABLE
		- 트랜잭션 순차 실행 – 여러 트랜잭션이 동시에 같은 행 접근 불가
		- 교착 상태 발생 가능, 성능 떨어짐
	- REPEATABLE_READ : 팬텀 리드
		- 하나의 트랜잭션이 수정한 행은 수정 불가 but 새 행 추가 가능
	- READ_COMMITTED : 팬텀 리드, 반복 가능하지 않은 조회
		- 커밋 완료된 데이터만 조회 허용
		- 어떤 트랜잭션이 접근한 행을 다른 트랜잭션이 수정 가능
	- READ_UNCOMMITED : 팬텀 리드, 반복 가능하지 않은 조회, 더티 리드
		- 한 트랜잭션 커밋 전에 다른 트랜잭션이 접근 가능
- 팬텀 리드: 한 트랜잭션 내에서 동일 쿼리를 보냈을 때 결과가 다른 경우 -> 행이 다를 수도 있음
- 반복 가능하지 않은 조회 : 한 트랜잭션 내의 같은 행에 대한 조회 결과가 다른 경우
- 더티 리드 : 한 트랜잭션이 실행 중일 때 아직 커밋 안된 행의 데이터를 읽는 경우

#### 지속성 (Durability)
- 성공적으로 수행된 트랜잭션은 영원히 반영되게
- 체크섬, 저널링(로그 남기기)

### 무결성 (Integrity)
- 데이터의 정확성, 일관성, 유효성 유지하는 것
#### 종류
- 개체 무결성 : 기본키는 null 불가
- 참조 무결성 : 서로 참조인 관계인 테이블의 데이터는 항상 값이 일관되어야함
- 고유 무결성 : unique 조건이 주어진 경우 모두 고유한 값을 가져야함
- NULL 무결성 : NOT NULL 조건이 주어진 경우 해당 속성 값은 NULL 불가




## 정규화(Normalization)
- 존재 이유
	- 이상 현상(anomaly) 해결
	- 저장 공간 효율화
### 이상 현상 (Anomaly)
	- 갱신 이상 : 중복된 튜플들 중 하나만 갱신되어 나머지들은 아직 갱신 전 데이터를 유지하는 경우
	- 삭제 이상 : 필요한 데이터가 함께 삭제되는 것
	- 삽입 이상 : 하나의 필드 값이 NULL이 불가능하여 삽입이 안되는 경우
### 제 1정규형
- 모든 도메인이 atomic – 나눌 수 없어야함

|number|content|
|---------|-----------|
|1|a, b|
|2|c, d|

->  변화 후
|number|content|
|--------|--------|
|1|a|
|1|b|
|2|c|
|2|d|

### 제 2정규형
- 제 1정규형이면서 기본키가 아닌 모든 속성이 기본키에 완전 함수 종속적
- 1NF 이면서 복합 후보키가 없으면 자동으로 2 정규형

|이름|		기술|		근무지|
|----------|----------|-------------|
|A	|	B	|	서울|
|A	|	C	|	서울|
|A	|	D	|	서울|
|Z	|	X	|	경기도|

에서 {이름, 기술} 이 테이블의 기본키가 된다. 2 정규형을 만족하려면 기본키가 아닌 모든 속성이완전 함수 종속적이여야 한다. 즉, 이름, 기술 -> 근무지 여야 하는데 근무지는 사람 이름에만 종속적이다. 이름 -> 근무지 이므로 이는 2 정규형에 위배된다. 이를 분리하여

|이름|근무지| 
|-----------|------|
|A|서울|	
|Z|경기도|


|이름|기술|
|---|----------|
|A|B|
|A|C|
|A|D|
|Z|X|

라는 테이블로 나누면 왼쪽의 기본키는 {이름}, 오른쪽의 기본키는 {이름, 기술}이 된다. 따라서 2 정규형을 만족한다.

### 제 3정규형
- 2 정규형을 만족하고, 이행적 함수 종속이 없어야 함
- 이행적 함수 종속 : A -> B, B -> C 이면 A -> C 인것

|대회|우승|우승자 생년 월일|
|----|-----|--------|
|A|a|99.03.23|
|B|b|00.03.23|

의 경우 대회 -> 우승자 이고, 우승자 -> 생년 월일 이므로 대회 -> 생년 월일 이 성립하게 된다. 따라서 제 3정규형을 만족하지 않는다. 이를 분리하여 

|대회|우승자|	
|--|--|
|A|a|
|B|b|

|우승자|생년월일|
|-----|---------|
|a|99.03.23|
|b|00.03.23|

로 만들면 제 3정규형을 만족하게 된다.

### 보이스-코드 정규형 (BCNF)
- 제 3정규형을 만족함
- 결정자가 후보키가 아닌 함수 종속 제거

### 성능
- 정규형을 통해 테이블을 나눠도 성능은 좋아질수도 나빠질수도
- why? 테이블을 나누면 그만큼 조인을 해야 하는 경우가 많아지므로



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
