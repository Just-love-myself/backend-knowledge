# HTTP Server

## HTTP 서버

1. 서버는 접속 요청을 받기 위한 소켓을 연다. → Listen
2. 클라이언트는 소켓을 만들고, 서버에 접속을 요청한다. → Connect
3. 서버는 접속요청한 클라이언트와의 통신을 위한 소켓을 따로 만든다. → Accept
4. 소켓을 통해 서로 데이터를 주고 받는다. → Send & Receive ⇒ 반복!
5. 통신을 마치면 소켓을 닫는다. → Close ⇒ 상대방은 Receive로 인지할 수 있다.

**1, 3, 4, 5번 구현하면 된다**

내 pc가 서버가 되는 것

## 1. Listen

다른 곳에 접속하는 게 아니라 포트 번호만 정하면 된다. 만약 80 포트를 사용 중이라면 8080 등 다른 포트 번호를 쓰면 된다.

```java
int port = 8080;
```

Java에서는 **ServerSocket**이라는 별도의 클래스를 사용한다(Socket을 상속한 게 아니라, 완전히 구별된다는 점에 주의).

> ServerSocket은 Listen의 기능만 수행.\
> 애초에 read\&write를 안 하니까 별도로 만들어준거다.

```java
ServerSocket listener = new ServerSocket(port);
```

클라이언트의 접속을 기다린다. 클라이언트가 접속하면 **통신용 소켓을 따로 만들어서 돌려준다. (3번 순서)**

```java
Socket socket = listener.accept();
```

I/O에서 이렇게 기다리는 걸 Blocking이라고 한다. 파일 읽기, 쓰기 등도 모두 Blocking 동작이지만, TCP 통신에서는 네트워크 상태 같은 요인에 의해 크게 지연될 수 있고, accept처럼 상대방의 요청이 없으면 영원히 기다리는 일이 벌어질 수 있다. 멀티스레드나 비동기, 이벤트 기반 처리 등이 필요한 이유다.

## 2. Request

서버는 요청을 하는 게 아니라, 요청을 받는다. 따라서 먼저 읽어야 한다.

코드는 위에서 다룬 것과 100% 동일하다.

```java
InputStreamReader reader = new InputStreamReader(socket.getInputStream());

CharBuffer charBuffer = CharBuffer.allocate(1_000_000);

reader.read(charBuffer);

charBuffer.flip();

System.out.println(charBuffer.toString());
```

## 3. Response

클라이언트의 요청과 마찬가지로, 응답 메시지를 만들어서 전송하면 된다.

```
HTTP/1.1 200 OK
(빈 줄)
Hello, world!
```

Java 코드

```java
String message = """
	HTTP/1.1 200 OK
	
	Hello, world!
	""";
```

또는

```java
String message = "" +
	"HTTP/1.1 200 OK\\n" +
	"\\n" +
	"Hello, world!\\n";
```

⚠️ 마지막 줄에 Newline(\n)을 넣는 걸 잊지 말자.

제대로 하려면 Content-Type과 Content-Length를 더해주는 게 좋다.

```java
String body = "Hello, world!";
byte[] bytes = body.getBytes();
String message = "" +
	"HTTP/1.1 200 OK\\n" +
	"Content-Type: text/html; charset=UTF-8\\n" +
	"Content-Length: " + bytes.length + "\\n" +
	"\\n" +
	body;
```

⚠️ Content-Length로 정확한 크기를 알 수 있기 때문에 마지막 줄에 Newline(\n)을 넣지 않아도 된다.

전송 코드는 클라이언트와 100% 동일하다.

```java
OutputStreamWriter writer = new OutputStreamWriter(socket.getOutputStream());

writer.write(message);
writer.flush();
```

## 4. Close

마찬가지로 클라이언트와 100% 동일하다.

