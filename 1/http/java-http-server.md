---
description: java에서 지원하는 API로 http server을 보다 쉽게 만들어보자
---

# Java Http Server

Java Http Server는내부적으로 NIO를 사용하는 고수준 HTTP 서버 API이다.

NIO란 : Non blocking Input Output이다. --> 기존 socket프로그래밍보다 고효율일것



## Http 서버 만들어보기

### 1. 서버 객체 준비

```java
InetSocketAddress address = new InetSocketAddress(8080);
int backlog = 0;

HttpServer httpServer = HttpServer.create(address, backlog);
```

* new InetSocketAddress(port number)를 통해 Address 설정
  * InetSocketAddress hostname or IP주소와 port 번호로 주소 설정 가능
* HttpServer.create(address, backlog)를 통해 서버 객체 만든다.

### 2. URL에 핸들러 지정

이곳에 Request와 Response를 어떻게 처리할 지 지정해주는 것.

람다식을 쓰지 않았을 때

```java
HttpHandler handler = new HttpHandler() {
            @Override
            public void handle(HttpExchange exchange) throws IOException {
                //To do
            }
        };
httpServer.createContext("/", handler);
```

**아샬 : "HttpHandler는 인터페이스인데다, 메서드 1개를 갖고 있기 때문에 람다식을 쓸 수 있다."**

```java
httpServer.createContext("/", (exchange) -> {
	// localhost:8080/ URL로 요청했을 때, 수행된다.
	// 만약 path를 "/hi"라고 하면, localhost:8080/hi로 요청왔을 때, 수행된다.
	// 1. Request 처리
	// 2. Response
});
```

### 함수형 인터페이스와 람다식

> #### 람다식 (Lambda Expression)
>
> 함수를 **하나의 식**(expression)으로 표현한 것이다. 함수를 람다식으로 표현하면 **메소드의 이름이 필요 없기** 때문에, 람다식은 익명 함수(Anonymous Function)의 한 종류라고 볼 수 있다.
>
> 문법 : (매개변수목록) -> {함수몸체}
>
> #### 함수형 인터페이스 (Functional Interface)
>
> **추상 메서드**를 딱 **하나** 갖고 있는 **인터페이스**를 말한다.
>
> 단, static 메서드나, default 메서드는 여러개 있어도 된다.
>
> **람다식**은 **함수형 인터페이스**의 **인스턴스**를 만드는 방법으로 사용될 수 있습니다.

<예제>

익명 내부 클래스 사용

```java
public class Foo {
    public static void main(String[] args) {
        //익명 내부 클래스(anonymous inner class)
        RunSomething runSomething = new RunSomething() {
            @Override
            public void doIt() {...}
        };
    }
}
```

람다식을 이용하여 간략히 표현

```java
public class Foo {
    public static void main(String[] args) {
        RunSomething runSomething = () -> System.out.println("Hello");
    }
}
```

### 3. Listen

```java
httpServer.start();
```

### 4. Request 처리

HTTP 메시지 구성요소 \[start-line, headers, 빈 줄, body] 들을 쉽게 뽑아낼 수 있다.

exchange란 매개변수를 통해 얻어온다. ex) exchange.getRequest\~\~()

**HTTP Method**

```java
String method = exchange.getRequestMethod();
System.out.println(method);
```

**HTTP path**

```java
URI uri = exchange.getRequestURI();
String path = uri.getPath();
System.out.println(path);
```

**Headers**\
key, value 형태로 되어있다.

```java
Headers headers = exchange.getRequestHeaders();
for (String key : headers.keySet()) {
	System.out.println(key + ": " + headers.get(key));
}
```

만약 특정 Header 값을 얻고 싶다면, 아래와 같이 **header.get(해당 key)**로 얻을 수 있다.

```java
List<String> strings = headers.get("Content-Length");
```

**Body**

우리가 Socket을 이용해서 HTTP 서버를 만들었을 때는, **socket.getInputStream()**을 통해 얻어왔지만 Java Http Server API에선 exchange를 통해 얻어온다.

```java
InputStream inputStream = exchange.getRequestBody();
Reader reader = new InputStreamReader(inputStream);
CharBuffer charBuffer = CharBuffer.allocate(1_000_000);
reader.read(charBuffer);  
```

InputStream에서 **readAllBytes()**를 통해 **byte 배열**을 얻을 수 있다.

```java
InputStream inputStream = exchange.getRequestBody();
String body = new String(inputStream.readAllBytes());
System.out.println(body);
```

Request HTTP 메시지 파싱 예시

<figure><img src="../../.gitbook/assets/server Request 정보 출력2.PNG" alt=""><figcaption></figcaption></figure>

### 5. Response

데이터를 Byte 배열로 준비.\
\-> **sendResponseHeaders()**의 인자로, body의 크기(byte)를 보내기 위해서다.

```java
String body = "Hello, world!";
byte[] bytes = body.getBytes();
```

HTTP Status Code와 Content-Length 지정. (필수)

반드시 getResponseBody() 호출전에 해야된다.&#x20;

```java
exchange.sendResponseHeaders(200, bytes.length);
```

데이터 전송.

```java
OutputStream outputStream = exchange.getResponseBody();
outputStream.write(bytes);
outputStream.flush();
```

## 코드 리팩터링

IntelliJ 기준 반복되는 부분 메서드로 리팩터링 하기

해당 부분 드래그해서 우클릭 -> Refactor -> Extract Method
