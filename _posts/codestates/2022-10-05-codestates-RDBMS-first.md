---
title : "코드스테이츠 학습일지 - 2022.10.05"

categories:
  - CodeStates
excerpt: "관계형 데이터베이스 - SQL"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-05
last_modified_at: 2022-10-05
---

## **Today I Learned**
📚오늘의 체크리스트
- [x] 스터디 미션
- [x] 금일 학습 분량
- [x] 금일 학습 이해

---

## 관계형 데이터베이스
### 데이터베이스의 필요성
- In-Memory
  - JavaScript에서 변수를 만들어 저장하는 경우 프로그램이 종료될 때 해당 프로그램이 사용하던 데이터도 사라짐
    : 변수 등에 저장한 데이터가 프로그램의 실행에 의존
    : -> 특정 상황에서 데이터의 보호가 불가능하고 데이터를 원하는 시간에 받아올 수 없으며 데이터의 수명이 프로그램의 수명에 의존
- File I/O
  - 파일을 읽는 방식으로 작동하는 형태
  - 엑셀 시트나 CSV 같은 파일의 형태는 In-Meomory에 비해 데이터를 저장하는 방식으로 적절해 보이지만 한계가 존재
    - 데이터가 필요할 때 마다 전체 파일을 읽어야 하여 파일 크키가 커질수록 비효율적
    - 파일이 손상되거나 여러 개의 파일을 동시에 다뤄야 하는 등 복잡하고 데이터량이 많아질수록 작없이 힘들어짐

- 관계형 데이터베이스에서는 하나의 CSV 파일이나 엑셀 시트를 한 개의 테이블로 저장 가능
  - 한번에 여러 개의 테이블을 가질 수 있기 때문에 SQL을 활용해 데이터를 불러오기 수월함

### SQL(Structured Query Language)
- SQL은 데이터베이스 용 프로그래밍 언어
- MySQL, Oracle, SQLite, PostgreSQL 등 다양한 데이터베이스에서 SQL 구문을 사용
- SQL은 구조화된 쿼리 언어
  - 쿼리(QUERY) : '질의문'이라는 뜻으로 저장되어 있는 데이터를 필터하기 위한 질의문으로 볼 수 있음
- 데이터베이스에 쿼리를 보내 원하는 데이터를 가져오거나 삽입 가능
- 데이터의 구조가 고정되지 않은 데이터베이스를 NoSQL이라하며 대표적으로 MongoDB와 같은 문서 지향 데이터베이스

#### SQL 기본 쿼리문
- Select
- Where
- And, Or, Not
- Order By
- Insert Into
- Null Values
- Update
- Delete
- Count
- Like
- Wildcards
- Aliases
- Joins
  - Inner Join
  - Left Join
  - Right Join
- Group By

#### 데이터베이스 관련 명령어
##### 데이터베이스 생성
```
CREATE DATABASE 데이버베이스_이름;
```
##### 데이터베이스 사용
```
USE 데이터베이스_이름;
```
##### 테이블 생성
- USE를 이용해 데이터베이스 선택 후, 테이블 생성 가능
- 테이블은 필드(표와 열)와 함께 만들어야 함
- 아래는 필드 조건 예시와 user라는 테이블을 만드는 예제

|필드 이름|필드 타입|그 외의 속성|
|:---:|:---:|:---:|
|id|숫자|Primary key이면서 자동 증가되도록 설정|
|name|문자열(최대 255개의 문자)||
|email|문자열(최대 255개의 문자)||

```
CREATE TABLE user (
  id int PRIMARY KEY AUTO_INCREMENT,
  name varchar(255),
  email varchar(255)
);
```

##### 테이블 정보 확인
```
DESCRIBE user;
```

- 확인 시 다음과 같이 user 테이블의 정보 확인 가능

```
mysqp> describe user;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(255) | YES  |     | NULL    |                |
| email | varchar(255) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
3  rows in set (0.00 sec)
```

#### MySQL에서 자주 사용하는 명령어
- SELECT : 데이터셋에 포함될 특성을 특정

```
SELECT 'hello world'    // 일반 문자열

SELECT 2                // 숫자

SELECT 15 + 2           // 간단한 연산
```

- FROM : 테이블과 관련한 작을을 할 경우 반드시 입력해야하며 FROM 뒤에는 결과를 도출해낼 데이터베이스 테이블을 명시

```
// 특정 특성을 테이블에서 사용하는 경우
SELECT 특성_1
FROM 테이블_이름

//몇 사기 특성을 테이블에서 사용하는 경우
SELECT 특성_1, 특성_2
FROM 테이블_이름

// 테이블의 모든 특성을 선택
// * 는 와일드카드(wildcard)로 전부 선택할 때 사용
SELECT *
FROM 테이블_이름
```

- WHERE : 필터 역할을 하는 쿼리문으로 선택적으로 사용 가능

```
// 특정 값과 동일한 데이터 찾기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 = "특정 값"

// 특정 값을 제외한 값을 찾기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 <> "특정 값"

// 특정 값보다 크거나 작은 데이터를 필터하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 > "특정 값"

SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_1 >= "특정 값"

// 문자열에서 특정 값과 비슷한 값을 필터하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 LIKE "%특정 문자열%"

// 리스트의 값들과 일치하는 데이터를 필터하기
SELECT 특성_1, 특성_2
FROM 테이블_이름
WHERE 특성_2 IN ("특정값_1", "특정값_2")

// 값이 없는 경우인 'NULL'을 찾기
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NULL

// 값이 없는 경우를 제외한 것을 찾기
SELECT *
FROM 테이블_이름
WHERE 특성_1 IS NOT NULL
```

- ORDER BY : 돌려받는 데이터 결과를 어떤 기준으로 정렬하여 출력할지 결정(선택적 사용)

```
// 오름차순(기본 정렬)
SELECT *
FROM 테이블_이름
ORDER BY 특성_1

// 내림차순
SELECT *
FROM 테이블_이름
ORDER BY 특성_1 DESC
```

- LIMIT : 결과를 출력할 데이터의 갯수를 정하는 쿼리문(선택적 사용, 가장 마지막에 추가)

```
SELECT *
FROM 테이블_이름
LIMIT 200
```

- DISTINCT : 유니크한 값을 받고 싶을 때 사용하는 쿼리문

```
// 특성_1을 기준으로 유니크한 값들만 선택하는 경우
SELECT DISTINCT 특성_1
FROM 테이블_이름

// 특성_1, 특성_2, 특성_3의 유니크한 '조합'값들을 선택하는 경우
SELECT
  DISTINCT
    특성_1
    ,특성_2
    ,특성_3
FROM 테이블_이름
```

- INNER JOIN : 둘 이상의 테이블을 서로 공통된 부분을 기준으로 연결

```
SELECT *
FROM 테이블_1
JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

- OUTER JOIN : 둘 이상의 테이블의 전체적인 부분을 연결
  - LEFT OUTER JOIN : 왼쪽 테이블의 모든 값이 출력
  - RIGHT OUTER JOIN : 오른쪽 테이블의 모든 값이 출력

```
// LEFT OUTER JOIN
SELECT *
FROM 테이블_1
LEFT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B

// RIGHT OUTER JOIN
SELECT *
FROM 테이블_1
RIGHT OUTER JOIN 테이블_2 ON 테이블_1.특성_A = 테이블_2.특성_B
```

### ACID
- 데이터베이스 내에서 일어나는 하나의 트랜잭션(transaction)의 안전성을 보장하기 위해 필요한 성질
  - 트랜잭션
    - 여러 개의 작업을 하나로 묶은 실행 유닛
    - 각 트랜잭션은 하나의 특정 작업으로 시작해 묶여 있는 모든 작업을 완료해야 정상적으로 종료
    - 여러 작업 중 단 하나의 작업시라도 실패하면, 트랜잭션에 속한 모든 작업을 실패한 것으로 판단
  - Atomicity(원자성)
    - 하나의 트랜잭션에 속해있는 모든 작업이 전부 성공하거나 전부 실패하도록 보장하는 성질
  - Consistency(일관성)
    - 데이터베이스의 상태가 일관되어야 한다는 성질
    - 트랜잭션이 일어난 이후의 데이터베이스는 데이터베이스의 제약이나 규칙을 만족해야 함
  - Isolation(격리성, 고립성)
    - 모든 트랜잭션은 다른 트랜잭션으로부터 독립되어야 함
  - Durability(지속성)
    - 하나의 트랜잭션이 성공적으로 수행되면 해당 트랜잭션에 대한 로그가 남아야 함
    - 만약 런타임 오류나 시스템 오류가 발생하더라도 해당 기록은 영구적이어야 함

### SQL vs NoSQL
- 관계형 데이터베이스
  - SQL을 기반으로 데이터를 다룸
  - 대표적으로 MySQL, Oracle, SQLite, PostgreSQL, MariaDB가 있음
  - 테이블의 구조와 데이터 타입 등을 사전에 정의하고 테이블에 정의된 내용에 알맞은 형태의 데이터만 삽입 가능
  - 테이블 간의 관계를 직관적으로 파악할 수 있음
- 비관계형 데이터베이스
  - NoSQL을 기반으로 데이터를 다룸
    - NoSQL
      - Key-Value 타입 : 속성을 Key-Value의 쌍으로 나타내는 데이터를 배열의 형태로 저장
        : Key는 속성 이름, Value는 속성에 연결된 데이터 값을 의미
      - 문서형(Document) 데이터베이스 : 데이터를 테이블이 아닌 문서처럼 저장하는 데이터베이스
        : 많은 문서형 데이터베이스에서 JSON과 유사한 형식의 데이터를 문서화하여 저장
        : 각각의 문서는 하나의 속성에 대한 데이터를 가지며 컬렉션이란 그룹으로 묶어서 관리
      - Wide-Column 데이터베이스 : 데이터베이스의 열(column)에 대한 데이터를 집중적으로 관리하는 데이터베이스
        : 각 열에 Key-Value 형식으로 데이터가 저장되고 컬럼 패밀리(column families)라는 열의 집합체 단위로 데이터를 처리 가능
        : 하나의 행에 많은 열을 포함할 수 있어 유연성이 높기 때문에 규모가 큰 데이터 분석에 주로 사용
      - 그래프(Graph) 데이터베이스 : 자료구조의 그래프와 비슷한 형식으로 데이터 간의 관계를 구성하는 데이터베이스
        : 노드(nodes)에 속성별(entities)로 데이터를 저장
        : 각 노드간 관계는 선(edge)으로 표현
  - 대표적으로 MongoDB, Casandra가 있음
  - 데이터가 고정되어 있지 않은 데이터베이스
  - 데이터를 읽어올 때 스키마에 따라 데이터를 읽어옴(schema on read)

#### SQL 기반의 데이터베이스와 NoSQL 데이터베이스의 차이점
- 데이터 저장(Storage)
  - SQL 기반 데이터베이스
    - SQL을 이용해 데이터를 테이블에 저장하며 미리 작성된 스키마를 기반으로 정해진 형식에 맞게 저장해야 함
  - NoSQL 데이터베이스
    - key-value, document, wide-column, graph 등의 방식으로 데이터를 저장

- 스키마(schema)
  - SQL 기반 데이터베이스
    - SQL을 사용하려면 고정된 형식의 시키마가 필요함(처리하려는 데이터 속성별로 컬럼에 대한 정보를 정해두어야 함)
    - 스키마는 수정 가능하지만 데이터베이스 전체를 수정하거나 오프라인으로 전환할 필요 있음
  - NoSQL 데이터베이스
    - 관계형 데이터베이스보다 동적으로 스키마의 형태를 관리할수 있음
    - 개별 속성에 대해 모든 열에 대한 데이터를 반드시 입력하지 않아도 됨

- 쿼리(Querying)
  - SQL 기반 데이터베이스
    - 정보를 요청할 때 SQL과 같이 구조화된 쿼리 언어를 사용
  - NoSQL 데이터베이스
    - 데이터 그룹 자체를 조회하는 것에 초점
    - 구조화되지 않은 쿼리 언어로 데이터 요청이 가능하며 UnQL(UnStructured Query Language)라고도 부름

- 확장성(Scalability)
  - SQL 기반 데이터베이스
    - 일반적으로 수직적으로 확장하며 높은 메모리, CPU를 사용하는 확장
    - 데이터베이스가 구축된 하드웨어의 성능을 많이 이용하여 비용이 높음
  - NoSQL 데이터베이스
    - 수평적으로 확장
    - 서버 증성 또는 클라우드 서비스를 이용하는 확장
    - 많은 트래픽을 보다 편리하게 처리할 수 있고 수직적 확장보다 상대적으로 비용이 저렴함

#### SQL 기반의 관계형 데이터베이스를 사용하는 케이스
1. 데이터베이스의 ACID 성질을 준수해야하는 경우
  : 전자 상거래를 비롯한 모든 금융 서비스를 위한 소프트웨어 개발
2. 소프트웨어에 사용되는 데이터가 구조적이고 일관적인 경우
  : 소프트웨어(프로젝트)의 규모가 많은 서버를 필요로 하지 않고 일관된 데이터를 사용하는 경우

#### NoSQL 기반의 비관계형 데이터베이스를 사용하는 케이스
1. 데이터의 구조가 거의 또는 전혀 없는 대용량의 데이터를 저장하는 경우
  : 소프트웨어 개발에 정형화되지 않은 많은 양의 데이터가 필요한 경우
2. 클라우드 컴퓨팅 및 저장공간을 최대한 활용하는 경우
  : 소프트웨어에 데이터베이스의 확장성이 중요한 경우
3. 빠르게 서비스를 구축하는 과정에서 데이터 구조를 자주 업데이트하는 경우
  : 스키마를 미리 준비할 필요가 없기 때문에 빠르게 개발하는 과정에 매우 유리함
  : 시장에 빠르게 프로토타입을 출시해야하는 경우에 해당


---

오늘보다 더 나은 내일이 될 수 있도록!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---