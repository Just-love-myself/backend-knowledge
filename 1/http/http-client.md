# HTTP Client



## HTTP 클라이언트

소켓 프로그래밍을 통해 직접 통신하는 걸 만들어보자. TCP 통신 순서를 다시 보자

1. 서버는 접속 요청을 받기 위한 소켓을 연다. → Listen
2. 클라이언트는 소켓을 만들고, 서버에 접속을 요청한다. → Connect
3. 서버는 접속요청한 클라이언트와의 통신을 위한 소켓을 따로 만든다. → Accept
4. 소켓을 통해 서로 데이터를 주고 받는다. → Send & Receive ⇒ 반복!
5. 통신을 마치면 소켓을 닫는다. → Close ⇒ 상대방은 Receive로 인지할 수 있다.

**클라이언트는 2번, 4번, 5번을 구현하면 된다.**

## 1. Connect

* 서버는 접속요청한 클라이언트와의 통신을 위한 소켓을 따로 만든다. → Accept
* 소켓을 통해 서로 데이터를 주고 받는다. → Send & Receive ⇒ 반복!

호스트는 IP 주소 또는 도메인 이름을 사용할 수 있다. 도메인의 경우 DNS를 활용하기 때문에 제대로 하려면 복잡해질 수 있지만, 알아서 처리해 준다.

* 통신을 마치면 소켓을 닫는다. → Close ⇒ 상대방은 Receive로 인지할 수 있다.

```java
String host = "example.com";
```

HTTP의 기본 포트 번호는 80.

```java
int port = 80;
```

IP 주소와 포트 번호만 알면, 서버에 접속할 수 있다.\
IP 주소로 기기에 접근, 여러 프로그램이 있을 있으니 포트 번호로 접근

```java
Socket socket = new Socket(host, port);
```

**객체 생성이지만 여기서 바로 서버에 접속 요청**한다. 실패하면 ConnectException 예외 발생.

## 2. Request

요청 메시지를 만들고, TCP로 전송하면 된다.

```
GET <http://example.com/> HTTP/1.1
(빈 줄)
```

또는

```
GET / HTTP/1.1
Host: example.com
(빈 줄)
```



> <mark style="background-color:orange;">**후자의 형태를 더 많이 쓴다.**</mark>\ <mark style="background-color:orange;">**마지막에 빈 줄을 넣는 걸 잊으면 안 된다.**</mark>

Java 코드

```java
String message = """
	GET / HTTP/1.1
	Host: example.com

	""";
```

> 해당 문법은 java text blocks를 검색하면 나온다.\
>

또는

```java
String message = "" +
	"GET / HTTP/1.1\\n" +
	"Host: example.com\\n" +
	"\\n";
```

소켓에서 Output Stream을 얻어서 쓸 수 있다. (Byte Array로 전송해야한다.)

```java
OutputStream outputStream = socket.getOutputStream();
outputStream.write(message.getBytes());
```

문자열을 직접 전송하고 싶다면 Writer를 쓴다(추천).

```java
OutputStreamWriter writer = new OutputStreamWriter(socket.getOutputStream());

writer.write(message);
writer.flush();
```

> &#x20;** **<mark style="background-color:orange;">**내부적으로 버퍼가 있기 때문에 flush를 잊지 않아야 한다.**</mark>

## 3. Response

소켓에서 Input Stream을 얻어서 쓸 수 있다.

```java
InputStream inputStream = socket.getInputStream();
```

Byte 배열을 준비하고, 여기로 데이터를 읽어온다. 응답 데이터가 우리가 준비한 배열보다 클 수도 있는데, 이 경우엔 반복해서 여러 번 읽는 작업이 필요하다. 이 경우엔 우리가 준비한 배열이 Chunk(한번에 처리하는 단위)가 된다. 단순하게 하기 위해 여기서는 한번만 읽는다.

```java
byte[] bytes = new byte[1_000_000];
int size = inputStream.read(bytes);
```

실제 데이터 크기만큼 Byte 배열을 자르고, 문자열로 변환해 출력한다.

```java
byte[] data = Arrays.copyOf(bytes, size);
String text = new String(data);

System.out.println(text);
```

요청과 마찬가지로 Reader를 쓰면 훨씬 편하다(추천).\
읽어와서 사이즈만큼 자르지 않아도 된다. CharBuffer가알아서 해준다.

```java
InputStreamReader reader = new InputStreamReader(socket.getInputStream());

CharBuffer charBuffer = CharBuffer.allocate(1_000_000);

reader.read(charBuffer);

charBuffer.flip();

System.out.println(charBuffer.toString());
```

reader.read() 할 때, charBuffer에 write를 했는데 toString을 하기 위해 다시 또 charBuffer에서 읽어와야한다. 그래서 flip()을 써야한다??

> <mark style="background-color:orange;">**CharBuffer에서 읽기 전에 flip을 잊지 않아야 한다.**</mark>

> 받은 데이터가 html형식이면 페이지가 뜨는 것

## 4. Close

더이상 할 게 없으니 이제 close하면 된다.

```java
socket.close();
```

Socket은 Closeable이기 때문에 try-with-resources를 써도 된다.

```java
try (Socket socket = new Socket(host, port)) {
	// Request
	// Response
}
```

> try-with-resources에 대해서 좀 더 알아보고 싶다면 아래 링크를 참고하자\
> [https://ducktopia.tistory.com/m/79](https://ducktopia.tistory.com/m/79)

