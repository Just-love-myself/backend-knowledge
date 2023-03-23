---
description: DTO가 무엇인지, 등장한 배경에 대해서 알아보자
---

# DTO

## IPC (Inter-Process-Communcation)

프로세스끼리 통신하는 것

F/E는 사용자의 입력을 받아 B/E에 요청하고, B/E는 요청을 처리해 응답한다.\
이 응답을 F/E는 사용자에게 적절한 형태로 보여주게 되고, 사용자는 다시 다른 입력을 하게 된다.&#x20;

이처럼 Tier를 나누면 IPC가 필수적이다.

IPC 구현 기술

* File → 가장 기본적인 접근. 원격 환경에서 활용하기 어렵다.
* Socket → 파일과 유사하게 읽고 쓸 수 있지만, 원격 환경에서도 활용할 수 있다.
  * HTTP 같은 고수준 프로토콜을 활용하면 어느 정도 정해진 틀이 있기 때문에 상대적으로 쉬워진다.
  * REST를 활용하면 RPC(SOAP의 일반적인 활용)가 아닌 Resource에 대한 CRUD로 정리해야한다.\
    (상대적으로 액션을 제한하는 것 -> why? REST에선 표현을 다뤄야하기 때문)
* Java에선 RPC를 위해 RMI(Remote Method Invocation)란 기술을 제공한다.
  * [RPC](https://ko.wikipedia.org/wiki/%EC%9B%90%EA%B2%A9\_%ED%94%84%EB%A1%9C%EC%8B%9C%EC%A0%80\_%ED%98%B8%EC%B6%9C)
  * [RMI](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94\_%EC%9B%90%EA%B2%A9\_%ED%95%A8%EC%88%98\_%ED%98%B8%EC%B6%9C)

### RPC

우리가 new 연산자를 통해 객체를 생성하고 메서드를 실행하면 우리 메모리, 컴퓨터에만 존재한다.

RPC는 원격으로 프로시저(=메서드)를 호출하는 것.&#x20;

비유하자면, 내 컴퓨터에 있는 메서드를 실행하면 원격에 있는 걸 실행해서 결과를 받아오는 식이다.\
즉, 원격에 있는 객체를 내 로컬에 있는 객체처럼 사용할 수 있다.

## DTO가 등장한 배경

앞에서 IPC에 대해서 배웠다.&#x20;

다른 프로세스와 통신하는데 우린 원격(네트워크를 통해)으로 통신한다.

Remote Facade와 같은 **remote interface**를 통해 작업할 때, DTO가 등장한 것.&#x20;

<mark style="background-color:orange;">DTO가 왜 필요했던걸까?</mark>

Remote Facade 객체 지향 모델에선 작은 메서드를 가지는 작은 객체를 사용하는 것이 좋다.\
\-> 응용 프로그램을 이해하기 쉽게 만들기 위해 좋은 의도를 드러내는 명명을 사용할 수 있다.

즉, 세분화된 동작들은 개체 간에 많은 상호작용이 있다는 뜻이고, 이러한 상호작용에는 일반적으로 많은 메서드 호출이 필요하다.

문제는 여기서 발생한다. 단일 주소 공간(로컬) 내에서 세분화된 상호작용은 괜찮다해도 프로세스 간에 호출을 수행할 땐 그렇지 않다는 것이다.

원격 호출은 해야 할 일이 많기 때문이다.

* 데이터 마샬링을 해야한다.
* 보안 관련 이슈를 확인해야한다.
* 스위치를 통해 패킷을 라우팅해야한다 등

즉, 프로세스 간 호출은 프로세스 내 호출보다 훨씬 **비용이 많이 든다는 것**이다.

따라서 우리는 결과적으로 **상호작용 수를 줄여야한다**. -> 그럴려면 각 통화에서 **더 많은 데이터를 전송**해야한다.

많은 매개변수를 사용한다는 방법이 있는데 Java와 같은 언어에선 단일 값만 반환하기 때문에 불가능하다.

통화에 관한 모든 데이터를 보유할 수 있는 객체를 만드는 것이다. -> "**데이터 전송 객체**(DTO)"

DTO를 사용하면 여러 호출을 단일 호출로 일괄 처리할 수 있을 뿐 아니라,\
데이터를 전송하기 위한 직렬화 메커니즘(ex. XML, JSON)을 캡슐화하는 이점이 있다.

직렬화를 캡슐화 함으로써 DTO는 직렬화를 변경할 수 있는 명확한 지점을 제공한다.&#x20;

물론, "데이터 전송"이란 측면에 집중하면 원격(remote)이 아닌 경우에도 DTO를 사용할 수 있다.

## DTO

이제 DTO에 대해서 하나씩 알아보자

DTO는 JavaBeans에서 유래한 Java Bean 또는 그냥 Bean이라고 부르는 형태에 가깝다.\
따라서 Java Beans의 몇가지 관례를 DTO도 따른다.

* 참고 링크 : [JavaBeans](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94%EB%B9%88%EC%A6%88)
* 클래스는 직렬화되어야 한다.(클래스의 상태를 지속적으로 저장 혹은 복원 시키기 위해)
* 클래스는 기본 생성자를 가지고 있어야 한다.
* 클래스의 속성들은 _get_, _set_ 혹은 표준 명명법을 따르는 메서드들을 사용해 접근할 수 있어야 한다.
* 클래스는 필요한 이벤트 처리 메서드들을 포함하고 있어야 한다.

## 무기력한 도메인 모델(Anemic Domain Model)

도메인 객체들에비즈니스 로직(확인, 계산, 비즈니스 규칙 등)이 거의 없거나 아예 없는 소프트웨어 도메인 모델의 이용이다.

객체지향 디자인에서의 객체들은 서로 책임이 있고, 협력해야하는데\
DTO는 그런게 하나도 없다. 그냥 데이터 덩어리일 뿐이다.

\-> 이러한 안티패턴을 마틴 파울러가 무기력한 도메인 모델로 명명함

## Java의 record 클래스

record란?

DTO 클래스 대신, record 타입으로 클래스 생성하면 된다.

* Record는 enum과 같은 특별한 형태의 class, 주로 간단하게 데이터를 저장하고 옮길 때 사용
* 불변(immutable) 데이터 객체를 쉽게 생성할 수 있도록 하는 새로운 유형의 클래스
* JDK14에서 preview로 등장하여 JDK16에서 정식 스펙으로 포함
* 일반 class와는 다르게 생성자, getter, hashCode(), equals() ,toString()를 제공
* 물론 custom이 필요하다면 record안에 정의해서 사용할 수 있다.

recode형 User class 정의

```java
public record User(Long id, 
        String Name,
        String email, 
        int age) { }
```

아래처럼 쓸 수 있다.

```java
public class RecordExample {
  public static void main(String[] args)
  {
    User user = new User
        (1l, "judy", "judy@example.com", 30);
 
    System.out.println(user.id());
    System.out.println(user.email());
 
    System.out.println(user);
  }
}
```

특징

1. 생성자는 모든 field를 포함합니다
2. toString()도 모든 field를 포함합니다.
3. equals(), hashCode() 메서드는 invokedynamic based mechanism을 사용합니다.
4. getter는 field이름과 유사한 이름으로 생성됩니다 ex) id(), email()..
5. 기본적으로 java.lang.Record class를 상속받기 때문에 다른 class를 상속받을 수 없습니다.
6. class가 final이기 때문에 다른 subclass를 생성할 수 없습니다.
7. 다른 클래스를 상속받을 수 없다. 인터페이스 구현은 가능하다.
8. 모든 field는 불변이기 때문에 setter는 제공하지 않습니다.



* 자바빈즈(JavaBeans)
* EJB(Enterprise JavaBeans)
* DAO
* ORM
