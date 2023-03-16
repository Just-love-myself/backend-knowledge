---
description: REST에 대해서 자세히 알아보자
---

# REST API

## REST API

REST 아키텍처의 제약 조건을 준수하는 웹 API를 RESTful API라고 합니다.

6가지 주요 제약 조건을 준수했을 때 RESTful API라고 간주할 수 있습니다.

REST는 Network-based Software Architecture를 위한 Architecture Style(=제약 조건의 집합)이다.\
Hybrid Architecture Style으로도 불린다. 왜냐면 REST의 제약조건이 각각 Architecture Style이기 때문

1. **Client-Server**

클라이언트-서버 아키텍처 스타일\
사용자 인터페이스 문제와 데이터 스토리지 문제를 분리하여 여러 플랫폼에서 사용자 인터페이스의 이식성을 향상시키고 서버 구성 요소를 단순화하여 확장성을 향상시킨다

2. **Stateless**&#x20;

클라이언트-스테이트리스-서버 스타일\
무상태여야한다 -> 클라이언트는 요청할 때마다, 요청을 이해하는데 필요한 모든 정보를 포함해야한다.

3. **Cache**&#x20;

클라이언트-캐시-스테이트리스-서버 스타일을 향상시키기 위해 캐시 제약조건 추가\
캐시 제약 조건은 요청에 대한 응답 데이터(ex.이미지, 동영상 등)가암묵적 또는 명시적으로 캐시 가능 또는 캐시 불가능으로 지정되어야 한다\
응답이 캐시 가능한 경우 클라이언트 캐시는 나중에 동등한 요청을 위해 응답 데이터를 재사용할 수 있는 권한을 부여받는다

4. **Layered System**

계층화된 시스템 스타일\
계층화된 시스템 스타일은 각 구성요소가 상호작용하는 직접적인 계층을 넘어서서 "보는" 것이 불가능하도록 구성요소 행동을 제한함으로써 계층화 계층으로 구성될 수 있게 한다.

5. **Code-On-Demand** (Optional)

스크립트 형태의 코드(ex. javascript)를 다운로드하고 실행하여 클라이언트 기능을 확장할 수 있다.\
이는 사전 구현에 필요한 기능의 수를 줄임으로써 클라이언트를 단순화하지만 가시성도 감소하므로 선택적 제약조건이다.

6. <mark style="color:red;">**Uniform Interface**</mark>





****



* 정보은닉(Information Hiding)과 캡슐화(Encapsulation)
  * 그리고 이 둘의 차이(많이들 혼용하니까 잘 알아두세요)
* Architecture와 Architecture Style의 차이
* REST(7가지 제약 조건 위주로 정리)
  * 교재에 나온 `필딩 제약 조건`을 좀 더 풀어서 정리해보세요.
  * 아래 2가지 자료를 보시면 도움이 되실 겁니다.
  * [https://www.youtube.com/watch?v=RP\_f5dMoHFc](https://www.youtube.com/watch?v=RP\_f5dMoHFc)
  * [https://blog.npcode.com/2017/03/02/바쁜-개발자들을-위한-rest-논문-요약/](https://blog.npcode.com/2017/03/02/%EB%B0%94%EC%81%9C-%EA%B0%9C%EB%B0%9C%EC%9E%90%EB%93%A4%EC%9D%84-%EC%9C%84%ED%95%9C-rest-%EB%85%BC%EB%AC%B8-%EC%9A%94%EC%95%BD/)
  * [https://blog.npcode.com/2017/04/03/rest의-representation이란-무엇인가/](https://blog.npcode.com/2017/04/03/rest%EC%9D%98-representation%EC%9D%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80/)
