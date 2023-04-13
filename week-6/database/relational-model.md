# Relational Model

Relational Model

관계형 모델에서 쓰이는 3개의 개념을 정확히 이해해야 한다:

1. Relation
2. Tuple
3. Attribute

### Attribute

속성은 이름과 타입으로 구성된다. 이름은 집합 안에서 유일해야 한다.

예를 들면,

* 이름/문자열
* 나이/정수
* 성별/문자

이런 게 속성이다.

대개는 Column으로 구현된다.

### Tuple

* (속성, 값) 쌍의 집합.&#x20;
* 하나의 집합에서 속성(Attribute) 이름은 유일하기 때문에, 속성 이름은 겹치지 않는다.

예를 들면 다음과 같다:

```
{ (이름/문자열, 견우), (나이/정수, 13), (성별/문자, 남) }
```

대개는 **Row, Record**로 구현된다.

<mark style="color:orange;">튜플은 집합이기 때문에 중복을 허용하지 않지만, 대부분의 RDBMS는 중복을 허용한다. 그리고 RDBMS는 NULL도 허용한다.</mark>

### Relation

* (속성의 집합, 튜플의 집합) 쌍.
* 속성의 집합을 heading, 튜플의 집합을 body라고 구분한다.&#x20;
* 단순하게 Relation은 “튜플의 집합”이라고 할 수도 있다.

예를 들면 다음과 같다:

```
(
	// Heading
	{ 이름/문자열, 나이/정수, 성별/문자 },

	// Body
	{
		{ (이름/문자열, 견우), (나이/정수, 13), (성별/문자, 남) },
		{ (이름/문자열,직녀), (나이/정수, 12), (성별/문자, 여) }
	}
)
```

관계는 시간에 따라 변하기 때문에, **관계 변수**(Relation Value)란 개념을 구분해서 사용할 수 있다.

대개는 Table로 구현되고, 속성 집합을 Schema로 표현한다

* ex) 관계 변수 : Table name
* ex) 관계 변수의 값 : Table

<figure><img src="../../.gitbook/assets/Relational_model_concepts.png" alt=""><figcaption><p>relational model</p></figcaption></figure>
