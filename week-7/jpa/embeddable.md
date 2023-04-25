# Embeddable

## Embeddable

* **Aggregate Mapping**이라 부른다.
* OOP에선 Primitive Type 보다는 Value Object를 권장한다.
  * ex) \
    String name\
    String gender 둘 다 String이면 구분이 불가능하다. 타입이 드러나게 해주자. (Gender gender)
* Embeddable은 Value Object를 지원한다.
* Embeddable을 쓰게되면 SRP(Single Responsibility Principle) 원칙을 따르게 된다.
  * Person이 Gender(@Embeddable)에게 Gender 관련 로직들을 위임할 수 있다.&#x20;

## Aggregate Mapping

DDD에서 Aggregate란 개념이 등장하는 것 같다.

간단히 말하자면, 비즈니스의 기능이 이동, 운송, 배달 등이면 우린 자동차가 필요하다.&#x20;

타이어, 엔진이 아닌 자동차가 말이다.

여기서 자동차가 Aggregate이고 타이어, 엔진이 Entity나 VO가 된다.

Aggregate를 통해&#x20;

참고 링크 : [Aggregate](https://incheol-jung.gitbook.io/docs/study/ddd-start/3)



## Value Object

기본 특성 3가지가 있다

1. Immutability(불변성) - 수정자가(setter) 없다
2. value equality(값 동등성) - 내부 값 동등성 검사
3. self validation(자가 유효성 검사) - 생성자에서 validate

각 특성에 대해서 알아보자

참고 링크 : [Value Object](https://velog.io/@livenow/Java-VOValue-Object%EB%9E%80)

### 정적 팩토리 메서드

VO를 조작할 때, 정적 팩토리 메서드 패턴을 활용하여 생성해야된다.

기존 생성자는 모두 private하게 바꾼다.

정적 팩토리 메서드는 무엇이고 왜 쓰게 되었을까?

정적 팩토리 메서드는 new를 간접적으로 사용하여 객체를 생성하는 메서드다.

```java
@Embeddable
public class Gender {
    @Column(name = "gender")
    private String value;

    //java bean이라 기본생성자 필요하지만, public으로 안 잡아주는게 좋다.
    private Gender() {
    }
    
    //client는매개변수 String type만으로 뭘 나타내는지 모른다.
    private Gender(String value) {
        this.value = value;
    }

    //정적 팩토리 메서드
    public static Gender male() {
        return new Gender("남");
    }
    //메서드 이름을 통해 반환될 객체의 특성을 묘사할 수 있다.
    public static Gender female() {
        return new Gender("여");
    }
}
```

이펙티브 자바라는 책을 읽어보면 정적 팩토리 메서드가 생성자 방식과 다른 점 5개가 있다고 한다.&#x20;

> 1. 이름을 가질 수 있다.
> 2. 호출 될 때마다 인스턴스를 새로 생성하지 않아도 된다.
> 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.
> 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.
> 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이게 각각의 장점이 무엇을 말하는지 코드를 통해 알아 보자

참고링크 : [정적 팩토리 메서드 탄생 배경](https://velog.io/@cjh8746/%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9CStatic-Factory-Method)

### equals() & hashcode()

성별이 같은지 비교하기 위해 equals()와 hashcode() 둘 다 재정의해줘야만 했다.

Gender 클래스에 적어주었는데, 왜 오버라이딩을 해줘야 하는지 알아보자.

결론부터 말하자면,

객체비교는 그 객체가 VO(Value Object)일 때 한다.\
VO에서의 필드값과 해쉬코드값을 동등성, 동일성을 비교해줄 때 오버라이딩(overriding)해서 사용한다.

**동등성** : 객체가 **주소값**이 다르더라도 **속성**(필드 값)이 같다면 같다고 보는 것. ex) equals()\
**동일성** : 객체가 **주소값**이 다르면 아무리 같은 내용이더라도 같지 않다고 보는 것. ex) ==

즉, VO 비교를 위해서는 값만 비교하면 되기에 (=**동등성** 비교를 위해) **원래 equals() 기능**(Object기능의 equals()는 동일성 비교를 한다)을 재정의해줘야 한다!

그리고 equals()메서드를 오버라이딩했다면 또다른 문제가 생길 수 있기에 hashCode()도 같이 오버라이딩할 수밖에 없는 이유를 설명해보겠다.

참고 링크 : [equals() & hashcode()](https://velog.io/@mooh2jj/equals%EC%99%80-hashCode%EB%8A%94-%EC%96%B8%EC%A0%9C-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94%EA%B0%80)\


## JPA 어노테이션

* @Entity&#x20;
* @Table
* @Id
* @Embeddable
* @Embedded



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
