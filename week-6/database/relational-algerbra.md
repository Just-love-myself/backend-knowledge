# Relational Algerbra

## Relational Algerbra

* 하나 이상의 Relation 연산 -> 새로운 Relation
* "하나 이상의 Relation으로 새로운 Relation을 만들 수 있다."
  * 단항 연산: Selection, Projection, Rename, …
  * 이항 연산: Cartesian Product, Set Union, Set Difference, …



### Projection

people(관계변수)이란 이름의 Table에서 name, age, gender라는 Attribute에 해당하는 값들을 tuple의 집합으로 불러올 것.

```sql
SELECT name, age, gender
FROM people;
```

```sql
SELECT *
FROM people;
```

### Selection

* SQL에선 SELECT문의 WHERE절로 술어를 표현할 수 있다.
* people이란 Table에서 age가 13 미만인 데이터 name, age, gender을 불러와라

```sql
SELECT name, age, gender
FROM people
WHERE age < 13;
```

### Cartesian Product

* Cartesian product만 하면 Tuple들 합쳐주는게 다다.
* 그래서Join과 같이 많이 쓴다.

```sql
SELECT items.name AS name, usage, people.gender 
FROM items 
JOIN people 
ON items.person_name = people.name;
```

* Outer Join이란 것도 있는데, 조건에 맞지 않아도 행 출력하는 명령어다.
* FROM 뒤에 나오는 관계변수가 기준이라고 생각하면 된다.(좀 더 무게를 두는 것)
* ex) item 목록 얻을 건데, 소유한 사람 이름도 알고 싶을 때 위 sql문을 작성한다.
