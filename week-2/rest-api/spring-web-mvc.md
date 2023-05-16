# Spring Web MVC로 구현

## Spring  Web MVC Annotation

* **@RequestMapping**

URL 을 컨트롤러의 메서드와 매핑할 때 사용하는 어노테이션

요청 주소(url) 설정, 요청 방식(GET, POST, DELETE, PATCH) 설정

요청방식들을 동시에 설정 가능

value : 연결할 url\
보통 호스트 주소와 보트 번호를 제외하고 api 설계 규약에 따라 이름을 짓는다.

method : HTTP Method

```java
@RequestMapping(value = "/test7", method = {RequestMethod.GET, RequestMethod.POST})
```

```java
@RequestMapping(value = {"/hello", "/hello-basic"})
```

* **@GetMapping**

GET Method를 처리하는 애노테이션

* **@PostMapping**

POST Method를 처리하는 애노테이션

* **@PatchMapping**

PATCH Method를 처리하는 애노테이션

* **@DeleteMapping**

DELETE Method를 처리하는 애노테이션

위에 GetMapping \~ DeleteMapping은 모두 들어가보면 @RequestMapping이 있다.

```java
@RequestMapping(method = RequestMethod.GET)
public @interface GetMapping {}
```

* **@PathVariable**

**사용법**

Controller에서 아래와 같이 작성하면 간단하게 사용 가능하다.

1. @GetMapping(PostMapping, PutMapping 등 다 상관없음)에 {변수명}
2. 메소드 정의에서 위에 쓴 변수명을 그대로 @PathVariable("변수명")&#x20;
3. (Optional) Parameter명은 아무거나 상관없음(아래에서 String name도 OK, String employName도 OK)

```java
@RestController
public class MemberController { 
    // 기본
    @GetMapping("/member/{name}")
    public String findByName(@PathVariable("name") String name ) {
        return "Name: " + name;
    }
    
    // 여러 개
    @GetMapping("/member/{id}/{name}")
	public String findByNameAndId(@PathVariable("id") String id, @PathVariable("name") String name) {
    	return "ID: " + id + ", name: " + name;
    }
    
}
```

* **@RequestParam**&#x20;

URI의 queryString을 받기에 적절한 애노테이션

required란 속성을 쓸 수 있다.

required = false -> query String에 파라미터가 없어도 된다는 뜻

```java
@RestController
public class MemberController { 
    // 기본
    //URI 예시 http://localhost:8080/member?name=김도균
    @GetMapping("/member")
    public String findByName(@RequestParam(value = "name") String name ) {
        return "Name: " + name;
    }
    
    // 여러 개
    //URI 예시 http://localhost:8080/member?name=김도균&age=24
    @GetMapping("/member/{id}/{name}")
	public String findByNameAndId(@RequestParam(value = "name)" String name,
	 @RequestParam(value = "age" int age) {
    	return "ID: " + id + ", name: " + name;
    }
    
}
```

* **@RequestBody**

클라이언트에서 서버에 JSON 형식의 requestBody로 요청 데이터를 전송했을 때, Java에서는 해당 JSON 형식의 데이터를 받기 위해서 JSON -> Java Object로의 변환이 필요하다.

이러한 과정을 @RequestBody 애노테이션이 처리해준다.

* **@ExceptionHandler**

@ExceptionHandler 는 @Controller , @RestController 가 적용된 Bean 에서 발생하는 예외를 잡아서 하나의 메서드에서 처리해주는 기능이다.

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface ExceptionHandler {

	/**
	 * Exceptions handled by the annotated method. If empty, will default to any
	 * exceptions listed in the method argument list.
	 */
	Class<? extends Throwable>[] value() default {};

}
```

value 설정을 통하여 어떤 예외를 잡을지 설정할 수 있다.&#x20;

주의할 점은 value 를 지정하지 않으면 모든 예외를 잡기 때문에 필요한 예외를 설정 해주어야한다.&#x20;

필요하다면 List형태로 여러 예외들을 정할 수 있다. \
`@ExceptionHandler({Excpetion.class, Exception2.class})` \
이런 방법으로 2개 이상도 등록 가능하다.

* **@ResponseStatus**&#x20;

응답 상태코드를 정해주는 애노테이션이다.

* **@RequestAttribute**

@RequestAttribute는 request 객체의 attribute를 가져오는 어노테이션이다.

```java
@GetMapping
public String sample(@RequestAttribute String userId){
    System.out.println(id);
}
```

인증/인가에서 HandlerIntercepter의 preHandle() 메서드로 HttpServletRequest 객체 (request 객체)에 속성을 지정해준 뒤,\
controller 쪽에서 요청받은 request 객체의 속성으로 인가를 할지 말지 결정할 수 있다.

ex) 글 삭제는 자신이 썼던 글만 삭제할 수 있다. -> request 객체의 userId를 보고 결정한다.

## Path Parameter와 Query String

path parameter와 query string이 무엇인지 알아보고, 둘의 차이점을 알아보자.



