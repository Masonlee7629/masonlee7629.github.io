---
title : "코드스테이츠 학습일지 - 2022-11-01"

categories:
  - CodeStates
excerpt: "Spring MVC - JPA 기반 데이터 액세스 계층"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-11-01
last_modified_at: 2022-11-01
---

## Spring MVC - JPA(Java Persistence API)
> Java 진영에서 사용하는 ORM(Object-Relational-Mapping)기술의 표준 사양
> <br>
> <br>
> 표준 사양은 Java의 인터페이스로 사양이 정의되어 있기 때문에 JPA라는 표준 사양을 구현한 구현체는 따로 존재
> <br>
> <br>
> JPA 표준 사양을 구현한 구현체<br>
> -> Hibernate ORM, EclipseLink, DataNucleus 등이 있음

### 데이터 액세스 계층에서의 JPA 위치

![image](https://user-images.githubusercontent.com/105192204/199227797-a7ecea0d-16e9-4aa7-b9bd-64d72093ce04.png)

> 데이터 액세스 계층에서 JPA는 데이터 액세스 계층의 상단에 위치
> <br>
> <br>
> 데이터 저장, 조회 등의 작업은 JPA를 거쳐 JPA의 구현체를 통해 이루어지며 구현체 내부적으로 JDBC API를 이용해 데이터베이스에 접근

### JPA에서 P의 의미
> JPA에서 P는 Persistence의 약자로 영속성, 지속성이라는 뜻을 지님
> <br>
> <br>
> 무언가를 금방 사라지지 않고 오래 지속되게 한다는 것이 Persistence의 목적

#### 영속성 컨텍스트(Persistence Context)
> ORM은 객체와 데이터베이스 테이블의 매핑을 통해 엔티티 클래스 객체 안에 포함된 정볼를 테이블에 저장하는 기술
> <br>
> <br>
> JPA에선 테이블과 매핑되는 엔티티 객체 정보를 영속성 컨텍스트라는 곳에 보관
> <br>
> <br>
> 영속성 컨텍스트에 보관된 엔티티 정보는 데이터베이스 테이블에 데이터를 저장, 수정, 조회, 삭제하는데 사용
> <br>
> <br>
> EntityMamager를 통해 영속성 컨텍스트에 접근<br>
> -> EntityManager가 생성되면 논리적 개념인 영속성 컨텍스트가 1:1로 생성

![image](https://user-images.githubusercontent.com/105192204/199229089-97b708ee-706d-4b66-b9f8-5a58cd1c81cd.png){: width="70%", height="70%"}

#### 엔티티의 생명주기 및 JPA 동작 과정

![image](https://user-images.githubusercontent.com/105192204/199246707-8c91e289-211e-4b47-bfac-ca8eaeef6c79.png)

> - 비영속<br>
> 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태

```java
Member member = new Member("Mason");
```

> - 영속<br>
> 영속성 컨텍스트에 관리되는 상태
> <br>
> <br>
> EntityManagerFactory의 createEntityManager() 메서드로 EntityManager 객체를 얻을 수 있고 EntityManager 객체를 통해 JPA의 API 메서드를 사용할 수 있음
> <br>
> <br>
> em.persist(객체)를 호출하면 영속성 컨텍스트의 1차 캐시에 객체의 정보들이 저장되고 객체는 쓰기 지연 SQL 저장소에 INSERT쿼리 형태로 등록

```java
Member member = new Member("Mason");

EntityManager em = entityManagerFactory.createEntityManager();
em.persist(member);
```

> - 영속성 컨텍스트의 객체 확인<br>
> em.find(Member.class, 1l) 메서드로 조회<br>
> -> find(조회할 엔티티 클래스의 타입, 조회할 엔티티 클래스의 식별자 값)
> <br>
> <br>
> 영속성 컨텍스트에 식별자 값에 해당하는 객체가 존재하지 않으면 테이블에 직접 SELECT 쿼리를 전송

```java
Member resultMember = em.find(Member.class, 1L);
```

> - 영속성 컨텍스트와 테이블에 엔티티 저장<br>
> EntityManager 객체를 통해 Transaction 객체를 얻고 JPA는 transaction 객체를 기준으로 데이터베이스의 테이블에 데이터를 저장
> <br>
> <br>
> tx.begin() 메서드를 호출해서 transaction을 시작
> <br>
> <br>
> em.persist(member) 메서드로 영속성 컨텍스트에 객체 저장
> <br>
> <br>
> tx.commit() 메서드를 호출하는 시점에 영속성 컨텍스트에 저장된 객체를 데이터베이스의 테이블에 저장
> <br>
> <br>
> commit()을 하기 전까진 em.persist()를 통해 쓰기 지연 SQL 저장소에 등록된 INSERT 쿼리가 실행되지 않음<br>
> -> 테이블에 데이터가 저장되지 않음
> <br>
> <br>
> tx.commit() 메서드가 호출되면 JPA 내부적으로 em.flush() 메서드가 호출되어 영속성 컨텍스트의 변경 내용을 데이터베이스에 반영

```java
EntityManager em = entityManagerFactory.createEntityManager();
EntityTransaction tx = em.getTransaction();

tx.bigin();
Member member = new Member("Mason");

em.persist(member);

tx.commit();
```

> - 영속성 컨텍스트와 테이블에 엔티티 업데이트<br>
> 객체를 1차 캐시에 저장하고 객체를 데이터베이스의 테이블에 저장
> <br>
> <br>
> find() 메서드로 테이블에 저장된 객체를 영속성 컨텍스트의 1차 캐시에서 조회
> <br>
> <br>
> setter 메서드로 객체 정보를 변경
> <br>
> <br>
> commit()을 실행하여 쓰기 지연 SQL 저장소에 등록된 UPDATE 쿼리가 실행
> <br>
>> UPDATE쿼리 실행 과정<br>
>> 1. 영속성 컨텍스트에 엔티티가 저장되면 저장되는 시점의 상태를 가진 스냅샷을 생성
>> 2. 해당 엔티티의 값을 setter 메서드로 변경
>> 3. commit()을 하면 변경된 엔티티와 기존의 스탭샷을 비교
>> 4. 변경된 값이 있으면 쓰기 지연 SQL 저장소에 UPDATE쿼리를 등록하고 실행

```java
tx.begin();
em.persist(new Member("mason@gmail.com"));
tx.commit();

tx.begin();
Member member1 = em.find(Member.class, 1L);
member1.setEmail("masonmason@gmail.com");
tx.commit();
```

> - 영속성 컨텍스트와 테이블의 엔티티 삭제<br>
> 객체를 1차 캐시에 저장하고 객체를 데이터베이스의 테이블에 저장
> <br>
> <br>
> > find() 메서드로 테이블에 저장된 객체를 영속성 컨텍스트의 1차 캐시에서 조회
> <br>
> <br>
> em.remove(객체)를 통해 영속성 컨텍스트의 1차 캐시에 있는 엔티티를 제거 요청
> <br>
> <br>
> commit()을 실행하면 영속성 컨텍스트의 1차 캐시에 있는 엔티티를 제거하고 쓰기 지연 SQL 저장소에 등록된 DELETE 쿼리가 실행

```java
tx.begin();
em.persist(new Member("mason@gmail.com"));
tx.commit();

tx.begin();
Member member = em.find(Member.class, 1L);
em.remove(member);
tx.commit();
```

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---