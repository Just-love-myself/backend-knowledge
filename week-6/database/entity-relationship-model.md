# Entity-Relationship-Model

## ERM (Entity-Relationship-Model)

* 가장 인기있는 Conceptual Data Model
* ER 모델에서 Entity는 개별적으로 다룰 수 있는 데이터를 의미한다. (**행위가 없다**)
* Entity Type은 같은 Attributes를 가진 Entity의 집합이다(OOP의 Class와 유사하다).&#x20;
* ERD 등을 그릴 때 쓰이는 건 Entity Type이다(Class Diagram을 떠올려보자).
* <mark style="color:red;">ERD에서 Entity는,  OOP의 Entity랑 다르다</mark>
  * 데이터 모델에서의 Entity는 행위가 없다. 그냥 데이터일 뿐이다.
  * "객체지향의 사실과 오해"
    * Entity : 객체 ex)Person(class) -> 직녀(entity, instance)\
      id라는 필드(identifier)가 있다면, 직녀의 age란 필드의 값이 아무리 바뀌어도 id덕분에 같은 직녀라고 한다. 객체는 **식별성, 연속성**을 가진다.
    * Value Object : 값 객체 ex) 1달러. (값 자체가 identifier이다)
* 참고로 OOP, DDD에선 Entity를 연속성과 식별성이 있는 객체란 의미로 사용한다. \
  똑같이 Entity란 표현을 쓰지만 실제로는 완전히 다른 의미를 갖는다. \
  데이터 모델링과 객체 모델링은 목표도 다르고, 용어, 원칙 등도 다르니 주의하자.
* Entity Type은 같은 Attribute를 가진 Entity(= instance)의 집합이다.
  * ERD를 그릴 때 쓰이는 건 Entity Type이다.
  * Class Diagram처럼 말이다.

### ERD (Entity-Relationship-Diagram)

* 논리적 설계 전에 큰 도움이 된다. (Logical Data Modeling)
* 개념적 모델로 큰 그림을 일단 잡는다고 이해하면 된다.
* Entity : Account, Character, Region, ...
* Relationship : Has, Contains, Carrying, ...
* Attribute : AcctName, Password, ...
* 개념적 모델은 **말이 되게 해야한다. (= 자연스럽게 말로 할 수 있어야한다.)**
  * &#x20;하나의 Account가 여러 개의 Character를 가진다. (O)
  * Account, Character는 1:n 관계를 가진다란 말보다 이해하기 쉽게 말로 할 수 있어야한다.
* <mark style="color:green;">ERD를 완벽히 하려고 하지말자!</mark>
  * 그러면 프로그래밍이 데이터에 끌려가게 된다. 그것보단 객체지향적 설계에 더 무게를 두자!

<figure><img src="../../.gitbook/assets/MMORPG ERD.png" alt=""><figcaption><p>MMORPG ERD</p></figcaption></figure>

## 데이터베이스 정규화

참고링크 : [DB 정규화](https://mangkyu.tistory.com/110)

* 제 1 정규화
* 제 2 정규화
* 제 3 정규화
* BCNF 정규화
