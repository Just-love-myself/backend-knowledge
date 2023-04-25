# Database

## Database

* 데이터를 **구조화, 조직화**한 것이다.
* 최종 사용자 또는 응용 프로그램은 데이터베이스에서 데이터를 쉽게 찾을 수 있어야 하고, 효율적으로 조작(추가, 수정, 삭제)할 수 있어야 한다. 또한 접근 권한, 보안 문제를 다룰 수 있어야 한다.&#x20;
* 즉, 데이터베이스를 **관리하는 시스템이** 필요하다.

## DBMS (DataBase Management System)

* DBMS는 다수의 사용자들이 데이터베이스 내의 데이터를 접근할 수 있도록 해주는 소프트웨어 도구의 집합이다.&#x20;
* DBMS은 사용자 또는 다른 프로그램의 요구를 처리하고 적절히 응답하여 데이터를 사용할 수 있도록 해준다.
* DBMS는 언어를 제공한다 -> [DDL, DML, DCL](https://velog.io/@alicesykim95/DB-DDL-DML-DCL-TCL%EC%9D%B4%EB%9E%80)
* 가장 대중적 데이터베이스 언어는 관계 데이터베이스의 데이터베이스 언어 SQL이다.
  * DDL (Data Definition Language) : Schema
  * DML (Data Manipulation Language) : Query & Command
  * DCL (Data Control Language) : Grant, Revoke, Commit, Rollback
* Schema의 경우 Schema란 걸 강조하기 위해 DDL이란 표현을 자주 사용하니 기억해 둘 것.

## Data Model

* 앞서서 데이터를 구조화, 조직화한 것을 데이터베이스라고 했는데, **어떻게 구조화, 조직화를 하느냐**가 데이터를 어떻게 모델링 하느냐와 같은 의미다.
* 3가지 종류가 있다.
  * Conceptual Data Model (ex. entity relationship model)
  * Logical Data Model \
    (ex. **relational data models(인기)**, network data models and hierarchical data models)
  * Physical Data Model

### ERM(Entity-Relationship-Model) vs RM(Relation-Model)

Relationship과 Relation은 완전히 다른 단어다.

**Relationship**은 Entity간의 **관계**를 말하고, \
**Relation**은 tuple(=record)의 집합 또는 **Table**이라고 이해하면 된다.

ex) 한 회사원이 여러 프로젝트를 담당한다면 \
\-> "**Relationship** exists between employee and each Project"라고 말할 수 있을 것.

* [Relational vs Relation](https://jee00609.github.io/dbms/difference-of-relation-and-relationship-in-rdbms/)
