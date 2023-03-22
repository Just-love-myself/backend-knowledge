---
description: DTO에 대해서 알아보자
---

# DTO

## DTO가 등장한 배경

\*Remote Facade가 Facade Pattern + Remote 개념이 추가된건지?

Remote Facade 객체 지향 모델에선 작은 메서드를 가지는 작은 객체를 사용하는 것이 좋다.\
\-> 응용 프로그램을 이해하기 쉽게 만들기 위해 좋은 의도를 드러내는 명명을 사용할 수 있다.

즉, 세분화된 동작들은 개체 간에 많은 상호작용이 있다는 뜻이고, 이러한 상호작용에는 일반적으로 많은 메서드 호출이 필요하다.

문제는 여기서 발생한다. 단일 주소 공간 내에서 세분화된 상호작용은 괜찮다해도 프로세스 간에 호출을 수행할 땐 그렇지 않다는 것이다.

원격 호출은 해야 할 일이 많기 때문이다.

* 데이터 마샬링을 해야한다.
* 보안 관련 이슈를 확인해야한다.
* 스위치를 통해 패킷을 라우팅해야한다 등

프로세스 간 호출은 프로세스 내 호출보다 훨씬 비용이 많이 든다는 것이다.

따라서 우리는 결과적으로 상호작용 수를 줄여야한다. -> 그럴려면 각 통화에서 더 많은 데이터를 전송해야한다.

많은 매개변수를 사용한다는 방법이 있는데 Java와 같은 언어에선 단일 값만 반환하기 때문에 불가능하다.

통화에 관한 모든 데이터를 보유할 수 있는 객체를 만드는 것이다. -> "데이터 전송 객체(DTO)"

DTO를 사용하면 여러 호출을 단일 호출로 일괄 처리할 수 있다는 것 말고도 장점이 있다.

<mark style="background-color:blue;">데이터를 전송하기 위한 직렬화 메커니즘(ex. XML, JSON)을 캡슐화하는 이점이 있다는 것.</mark>

<mark style="background-color:blue;">직렬화를 캡슐화 함으로써 DTO는 직렬화를 변경할 수 있는 명확한 지점을 제공한다. -></mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">**이해 못함**</mark>



* DTO (Data Transfer Object) 란
  * 프로세스 간 통신(IPC, Inter-Process Communication)
* “무기력한 도메인 모델” 이란 그리고 안티 패턴인 이유
* 자바빈즈(JavaBeans)
* EJB(Enterprise JavaBeans)
* Java의 record
* DAO
* ORM
