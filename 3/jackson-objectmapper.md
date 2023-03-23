# Jackson ObjectMapper

## Jackson ObjectMapper

Jackson ObjectMapper를 사용해서 DTO를 JSON(문자열)으로 변환하거나, JSON(문자열)을 DTO로 변환할 수 있다.

먼저, DTO를 위한 class를 만든다. JSON의 스키마를 작성한다는 느낌으로 만들면 된다. 필드보다는 getter 메서드의 이름을 따른다는 점에 주의하고, 만약 이름을 강제하고 싶다면 @JsonProperty 애너테이션을 사용하자.

**JSON 스키마**

```json
{
	"id": "1234",
	"title": "제목",
	"content": "테스트"
}
```

**Java 코드**

```java
public class PostDto {
	private String id;

	private String title;

	private String content;

	public PostDto() {
	}

	public PostDto(String id, String title, String content) {
		this.id = id;
		this.title = title;
		this.content = content;
	}
	
	public String getId() {
		return id;
	}

	public String getTitle() {
		return title;
	}

	public String getContent() {
		return content;
	}
}
```



Spring DI를 통해 컨트롤러에서 Jackson ObjectMapper를 얻는다.&#x20;

스프링이 등록된 객체(Bean)를 관리하고 있고, 생성자 주입을 통해 얻어오자.

```java
public class PostController {
	private final ObjectMapper objectMapper;
	
	public PostController(ObjectMapper objectMapper) {
		this.objectMapper = objectMapper;
	}
```



Jackson ObjectMapper를 써서 DTO를 JSON 포맷의 **String**으로 변환한다.&#x20;

JsonProcessingException 예외가 발생할 수 있는데, \
여기서는 간단히 JacksonException 예외로 간단히 쓰겠다.

```java
	@GetMapping("/{id}")
	public String detail(@PathVariable String id) throws JacksonException {
		PostDto postDto = new PostDto(id, "제목", "내용");
		
		return objectMapper.writeValueAsString(postDto);
	}
```



JSON String으로 변환해서 반환하는 방법도 있지만\
DTO 객체 자체를 반환할 수도 있다.

그러면**Jackson**이 알아서 변환해준다.

Spring Boot가 Jackson을 지원하기 때문에 변환 또한 Spring Boot가 알아서 해준다.

\+) **List, Map을 반환해도 알아서 변환해준다.**

```java
	@GetMapping("/{id}")
	public PostDto detail(@PathVariable String id) throws JacksonException {
    PostDto postDto = new PostDto(id, "제목", "내용");

    return postDto;
	}
```



데이터를 전달하는 POST에서도 마찬가지로 Jackson ObjectMapper를 쓸 수 있다. 여기선 잘 전달됐는지 바로 확인하기 위해 DTO를 그대로 응답으로 활용해 보자.

```java
	@PostMapping
	@ResponseStatus(HttpStatus.CREATED)
	public PostDto create(@RequestBody String body) throws JacksonException {
		//읽어오고자 하는 body, 어떤 DTO클래스로 변환하고 싶은지 매개변수로 적어준다.
		PostDto postDto = objectMapper.readValue(body, PostDto.class);
		
		return postDto;
	}
```



여기서도 Spring Boot가 자동으로 변환하는 걸 써보자.

요청 메시지 바디 내용을 DTO형으로 받아왔다.

DTO 클래스에 정의되어있는 key-value 값이 없으면 null이고, 존재하면 값이 채워진다.

```java
	@PostMapping
	@ResponseStatus(HttpStatus.CREATED)
	public PostDto create(@RequestBody PostDto postDto) {
		return postDto;
	}
```

CRUD 모두 이런 식으로 DTO를 활용할 수 있다. 이때 적절한 요청 또는 응답을 처리하도록 DTO를 세분화해도 좋다. 예를 들어, Create(POST)에선 id를 직접 넘겨주지 않는다는 걸 명확히 드러낼 수도 있다.
