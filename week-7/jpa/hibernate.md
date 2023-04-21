# Hibernate

## Hibernate

* 하이버네이트는 자바 언어를 위한 ORM 프레임워크이고 JPA의 구현체로, JPA 인터페이스를 구현하고 내부적으로 JDBC API를 사용한다.
* 하이버네이트는 자바객체를 통해 데이터베이스가 Oracle, MySql, MSSQL 등 에 상관없이 다룰수 있도록 하는 추상화를 목표로 한다.
* Mybatis에 익숙한 사람들이라면, Mybatis 구조가 들어가는 자리에 Hiberante가 들어오는 구조라고 이해하면 된다.
* 이를 통해 개발자는 SQL를 직접 사용하지 않고 메서드 호출만으로 쿼리가 수행된다, 즉 SQL을 작성하는 시간을 줄여 생산성이 높아진다.&#x20;
* 하지만 직접 SQL을 작성하는것보다는 성능상 좋지 않고 세밀하게 데이터를 조작하기 힘들다.\
  이를 보안하기 위해 JPQL과 NativeQuery를 지원하고 Querydsl을 함께 사용할 수 있다.

## Persistence Context (영속성 컨텍스트)

### JPA 동작방식 <a href="#section-2-jpa-brbr" id="section-2-jpa-brbr"></a>

![JPA 동작 방식](https://velog.velcdn.com/images%2Fcodren%2Fpost%2F3eaacc52-8165-469f-8976-ddae78508d7f%2Fimage.png)

#### **1. Entity Manager Factory**

> 엔티티 매니저 인스턴스를 관리하는 주체

* Application 실행 시 한 개만 만들어짐
* 사용자로부터 요청이 오면 엔티티 매니저를 생성

#### **2. Entity Manager**

> Persistence Context 에 접근하여 DB 작업을 제공하는 객체

* 내부적으로 DB Connection 을 이용해서 DB 에 접근
* find() : 영속성 컨텍스트에서 엔티티를 검색하고 영속성 컨텍스트에 없을 경우 DB에서 데이터를 찾아 영속성 컨텍스트에 저장한다.
* persist() : 엔티티를 영속성 컨텍스트에 저장한다.
* remove() : 엔티티를 영속성 컨텍스트에서 삭제한다.
* flush() : 영속성 컨텍스트에 저장된 내용을 데이터베이스에 반영한다.

#### 3**. Persistence Context**

> Entity 를 영구 저장하도록 지원하는 환경으로써 엔티티 매니저를 통해 접근 가능함

### **Entity Life Cycle**

![엔티티 생명주기](https://velog.velcdn.com/images%2Fcodren%2Fpost%2Fd17715aa-9de8-46ab-813d-128712c04f33%2Fimage.png)

* 비영속 (new)\
  \- new 키워드를 통해 생성된 상태로 아직 영속성 컨텍스트에 저장되지 않음
* 영속 (managed)\
  \- 엔티티가 영속성 컨텍스트에 저장되어 관리되는 상태\
  \- **아직 DB 에 저장된 상태 X, 트랜잭션 Commit 후에 DB 에 반영 ( flush() 자동 호출 )**
* 준영속 상태 (detached)\
  \- 영속성 컨텍스트에 엔티티가 저장되었다가 분리된 상태
* 삭제 상태 (removed)\
  \- 영속성 컨텍스트와 데이터베이스에서 삭제된 상태

### **Persistence Context 사용 시 이점**

![1차 캐시](https://velog.velcdn.com/images%2Fcodren%2Fpost%2Faa287462-bfe0-45ac-a9d7-1a374c249d33%2Fimage.png)

* **1차 캐시**\
  \- 영속성 컨텍스트 내에 저장되는 캐시로 **Map\<KEY,VALUE>**로 저장됨\
  \- entityManager.find() 메소드 호출 시 1차 캐시 조회\
  \- 존재할 경우 반환, 없으면 DB 조회 후 1차 캐시에 저장 및 반환
* **동일성 보장**\
  \- 하나의 트랜잭션에서 같은 키 값으로 같은 엔티티 조회를 보장받음
* **트랜잭션을 지원하는 쓰기 지연**\
  \- entityManager.persist() 호출하면 1차 캐시에 저장과 동시에 지연 SQL 저장소에 저장\
  \- 트랜잭션 커밋 시점에 쌓인 SQL문들이 flush() 되면서 DB 에 반영\
  \- 한 번에 처리하기 때문에 성능 이점 (DB 연결을 한 번만 하니까)

<figure><img src="https://velog.velcdn.com/images%2Fcodren%2Fpost%2Fa8ad8406-b581-4a45-a964-1636a47754e4%2Fimage.png" alt=""><figcaption><p>쓰기지연 SQL 저장소</p></figcaption></figure>

* **변경 감지**\
  \- JPA 는 1차 캐시에 DB 에서 처음 불러온 **엔티티의 스냅샷**을 저장\
  \- 스냅샷과 비교하여 변경 내용이 있다면UPDATE SQL 수행

1. 트랜잭션이 시작된다.
2. ntityManager를 통해 영속성 엔티티를 찾아온다.
3. 이후 엔티티의 속성을 변경한다.
4. 트랜잭션이 종료되면서 정상 동작이면 commit한다.
5. JPA는 flush()를 한다.
6. flush는 영속성 컨텍스트에서 변경된 엔티티를 감지하여 update 쿼리를 데이터베이스에 전달하는 과정이다.

참고로, 변경감지 기능은 트랜잭션 안에서만 활성화된다. \
따라서 트랜잭션 밖에서 엔티티를 수정하면 아무 일도 일어나지 않는다.

## 트랜잭션

데이터베이스의 상태를 변화시키기 해서 수행하는 작업의 단위를 뜻한다.

### 트랜잭션 특징

트랜잭션의 특징은 크게 4가지로 구분된다.&#x20;

* 원자성 (Atomicity)
* 일관성 (Consistency)
* 독립성 (Isolation)
* 지속성 (Durability)

&#x20;**원자성**은 트랜잭션이 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다는 것이다. &#x20;

* 트랜잭션은 사람이 설계한 논리적인 작업 단위로서, 일처리는 작업단위 별로 이루어 져야 사람이 다루는데 무리가 없다.
* 만약 트랜잭션 단위로 데이터가 처리되지 않는다면, 설계한 사람은 데이터 처리 시스템을 이해하기 힘들 뿐만 아니라, 오작동 했을시 원인을 찾기가 매우 힘들어질것이다.

&#x20;**일관성**은 트랜잭션의 작업 처리 결과가 항상 일관성이 있어야 한다는 것이다.&#x20;

* 트랜잭션이 진행되는 동안에 데이터베이스가 변경 되더라도 업데이트된 데이터베이스로 트랜잭션이 진행되는것이 아니다
* 처음에 트랜잭션을 진행 하기 위해 참조한 데이터베이스로 진행된다. \
  이렇게 함으로써 각 사용자는 일관성 있는 데이터를 볼 수 있다

&#x20;**독립성**은 둘 이상의 트랜잭션이 동시에 실행되고 있을 경우 어떤 하나의 트랜잭션이라도, 다른 트랜잭션의 연산에 끼어들 수 없다는 점을 가리킨다.

* 하나의 특정 트랜잭션이 완료될때까지, 다른 트랜잭션이 특정 트랜잭션의 결과를 참조할 수 없다.

&#x20;**지속성**은 트랜잭션이 성공적으로 완료됬을 경우, 결과는 영구적으로 반영되어야 한다는 점이다.

## JPQL

EntityManager엔 여러 Entity를 얻을 수 있는 메서드가 없다. 이를 위해 JPQL과 createQuery를 사용할 수 있다. JPQL은 SQL과 다르게 관계(테이블)이 아니라 JPA 객체를 대상으로 하는 SQL과 유사한 쿼리(질의) 언어다.

```java
String jpql = "SELECT person FROM Person person";

List<Person> people = entityManager
	.createQuery(jpql, Person.class) // 타입을 지정하지 않으면 Query, 지정하면 TypedQuery.
	.getResultList();

assertEquals(2, people.size());
```
