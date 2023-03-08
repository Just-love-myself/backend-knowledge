# HTTP의 이해

## HTTP란?

HTTP는 HTML 문서와 같은 리소스들을 가져올 수 있도록 해주는 프로토콜이다.

{% hint style="info" %}
#### 프로토콜 : 규칙의 집합, 규약, 약속
{% endhint %}

<figure><img src="../../.gitbook/assets/http-layers.png" alt=""><figcaption><p>http-layer</p></figcaption></figure>



OSI 7 계층 중  2, 3, 4, 7계층만 살펴보자.

1. 2계층 - 데이터 링크 계층 ⇒ MAC address (물리적 고유 주소)
2. 3계층 - 네트워크 계층 ⇒ IP address
3. 4계층 - 전송 계층 → TCP, UDP ⇒ Port number (프로그램 별 부여)
4.  7계층 - 응용 계층 → HTTP 등

    1. HTTPS를 위한 TLS 같은 보안 계층이 먼저 들어갈 수도 있다.\
       (=TLS을 한 번 거쳐간다.)



클라이언트-서버 모델 + 메시지 교환

1. 서비스/리소스 -> 리소스를 특정하기 위해 URL을 사용한다.
2. 클라이언트 -> 요청
3. 서버 -> (처리 후)응답



## Stateless Protocol

예시

1. HTTP는 각각의 요청이 독립적이다.
2. 즉, 클라이언트는 항상 자신이 누군지 알려줘야한다.\
   따라서 아래 방법들로 보완한다.
   * 요청과 응답을 통해 계속 주고 받는 쿠키
   * 데이터는 서버에서 관리하고 쿠키 등으로 key를 관리하는 세션
   * 웹 브라우저의 기능(local storage 등)

## HTTP 메시지

1. 기본적으로는 사람이 읽을 수 있는 형태
2. 요청과 응답 모두 동일 구조
   1. Start line → 요청과 응답의 형태가 다름.
   2. Headers
   3. 빈 줄
   4. Body
      1. 크기를 알기 어렵다. Headers의 Content-Length 항목 등을 활용한다.
      2. 위와 다르게 꼭 사람이 읽을 수 있는 텍스트 형태일 필요는 없다. 바이너리 등 가능.
      3. 하나가 아니라 여럿일 수도 있다. 파일 업로드 등을 위해 쓰이는 multipart/form-data가 대표적.

## [HTTP Method](https://developer.mozilla.org/ko/docs/Web/HTTP/Methods) (요청)

1. GET → Read
2. HEAD → GET without body
3. POST → Submit (멱등성X) ⇒ Collection Pattern에서 Create로 사용
4. PUT → Update (+Create) ⇒ Overwrite!
5. PATCH → Update (partial) (멱등성X)
6. DELETE → Delete
7. OPTIONS → 지원 확인

## [HTTP Status Code](https://developer.mozilla.org/ko/docs/Web/HTTP/Status) (응답)

1. 1xx → 정보 ⇒ 우리가 직접 쓰는 일은 드믐.
2. 2xx → 성공 ⇒ 200 OK, 201 Created, 204 No Content
3. 3xx → 리다이렉션 ⇒ 304 Not Modified가 특수한 형태로 자주 보임.
4. 4xx → 클라이언트 쪽 문제 ⇒ 404 Not Found
5. 5xx → 서버 쪽 문제 ⇒ 500 Internal Server Error

## TCP와 UDP

전송 계층의 대표적인 프로토콜

* TCP: 연결이 필요함. 전달 및 순서 보장. (전화)
* UDP: 연결하지 않고 데이터를 보냄. 전달 및 순서를 보장하지 않음. (편지)

## Socket

* [Berkeley\_sockets](https://en.wikipedia.org/wiki/Berkeley\_sockets)
* [네트워크\_소켓](https://ko.wikipedia.org/wiki/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC\_%EC%86%8C%EC%BC%93)

Socket과 Socket API를 구분해야 헷갈리지 않는다.

Socket은 기본적으로 파일과 유사하게 다룰 수 있다(유닉스에서는 파일 디스크립터의 일종).

Java에서는 키보드 입력, 화면 출력, 파일 입출력 등과 마찬가지로 Stream(Java 8에서 도입된 Stream API가 아님에 주의!)으로 다룰 수 있다.

## TCP 통신 순서

1. 서버는 접속 요청을 받기 위한 소켓을 연다. → Listen
2. 클라이언트는 소켓을 만들고, 서버에 접속을 요청한다. → Connect
3. 서버는 접속 요청을 받아서 클라이언트와 통신할 소켓을 따로 만든다. → Accept
4. 소켓을 통해 서로 데이터를 주고 받는다. → Send & Receive ⇒ 반복!
5. 통신을 마치면 소켓을 닫는다. → Close ⇒ 상대방은 Receive로 인지할 수 있다.

2번, 3번 과정의 디테일을 추가해보자면, TCP는3 handshake 방식으로 서로 연결됐는지 확인한다.\
연결을 할 때마다 매번 추가 절차가 생겨, 속도는 느릴 수 있지만 확실하다.&#x20;
