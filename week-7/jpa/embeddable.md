# Embeddable

## Embeddable

* Aggregate Mapping이라 부른다.
* OOP에선 Primitive Type 보다는 Value Object를 권장한다.
  * ex) \
    String name\
    String gender 둘 다 String이면 구분이 불가능하다. 타입이 드러나게 해주자. (Gender gender)
* Embeddable은 Value Object를 지원한다.
* Embeddable을 쓰게되면 SRP(Single Responsibility Principle) 원칙을 따르게 된다.
  *   Person이 Gender(@Embeddable)에게 Gender 관련 로직들을 위임할 수 있다.&#x20;



## [Value Object](https://velog.io/@livenow/Java-VOValue-Object%EB%9E%80)

기본 특성 3가지가 있다

1. Immutability(불변성) - 수정자가(setter) 없다
2. value equality(값 동등성) - 내부 값 동등성 검사
3. self validation(자가 유효성 검사) - 생성자에서 validate

학습 키워드

* Aggregate Mapping
* [Value Object](https://velog.io/@livenow/Java-VOValue-Object%EB%9E%80)
  * [정적 팩토리 메서드 탄생 배경](https://velog.io/@cjh8746/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9CStatic-Factory-Method)
  * [equals() & hashcode()](https://velog.io/@mooh2jj/equals%EC%99%80-hashCode%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80)
* JPA 어노테이션
  * @Entity
  * @Table
  * @Id
  * @Embeddable
  * @Embedded
