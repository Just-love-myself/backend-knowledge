# CORS

참고 링크 : [https://evan-moon.github.io/2020/05/21/about-cors](https://evan-moon.github.io/2020/05/21/about-cors/)

웹 개발을 하다보면 CORS 정책 위반으로 인해 에러가 발생하는 상황은 굉장히 흔해서 누구나 한 번 정도는 겪게 된다

로컬 환경에서 클라이언트 어플리케이션을 만들고 있었고, 친구가 미리 만들어서 배포해놓은 개발 환경 API 서버와 통신을 시도했었다.

API 서버와 통신을 진행해서 데이터를 받아오면 되는 단순한 작업이었기 때문에 아무 생각없이 통신을 진행했는데, 갑자기 콘솔이 빨개지더니 당황스러운 메세지를 뱉어냈다.

> 🚨 Access to fetch at ‘https://api.lubycon.com/me’ from origin ‘http://localhost:3000’ has been blocked by CORS policy: No ‘Access-Control-Allow-Origin’ header is present on the requested resource. If an opaque response serves your needs, set the request’s mode to ‘no-cors’ to fetch the resource with CORS disabled.

이렇듯 우리가 겪는 CORS 관련 이슈는 모두 CORS 정책을 위반했기 때문에 발생하는 것이다.

## CORS 기법

CORS는 `Cross-Origin Resource Sharing`의 줄임말로, 한국어로 직역하면 교차 출처 리소스 공유라고 해석할 수 있다

일단 다른 출처간의 리소스 공유에 대해서 알아보기에 앞서 간단하게 이 `출처(Origin)`라는 것이 정확히 뭘 의미하는지부터 한번 짚고 넘어가도록 하자.

### 출처(Origin)

출처는 Scheme과 Host, Port를 합친 것을 의미한다.

> URL 문법
>
> scheme://\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]

따라서 출처가 같다는 건 Scheme과 Host, Port 세가지 모두가 동일해야한다는 것이다.

### SOP

웹 브라우저가 처리하는 보안 정책(서버에서는 이미 처리 끝내고 결과를 준 상태).

얻으려는 리소스의 출처(Host+Port)가 현재 페이지(B/E 입장에서는 요청하는 쪽)의 출처(Origin)와 다르면 접근할 수 없게 하는 보안 정책.&#x20;

참고로 **출처를 비교하는 로직**이 서버에 구현된 스펙이 아니라 **브라우저**에 구현되어 있는 스펙

따라서 브라우저를 통하지 않고 서버 간 통신을 할 때는 이 정책이 적용되지 않는다.

<figure><img src="https://evan-moon.github.io/static/d4d623ba331c1d7851e7000c11cd3809/6af66/cors.png" alt=""><figcaption><p>서버는 CORS를 위반하더라도 정상적으로 응답을 해주고, 응답의 파기 여부는 브라우저가 결정한다</p></figcaption></figure>

### CORS은 어떻게 동작하는가

어떤 방법을 통해 서로 다른 출처를 가진 리소스를 안전하게 사용할 수 있는지 알아보자

기본적으로 웹 클라이언트 어플리케이션이 다른 출처의 리소스를 요청할 때는 HTTP 프로토콜을 사용하여 요청을 보내게 되는데, 이때 브라우저는 요청 헤더에 `Origin`이라는 필드에 요청을 보내는 출처를 함께 담아보낸다.

```http
Origin: https://evan-moon.github.io
```

이후 서버가 이 요청에 대한 응답을 할 때 응답 헤더의 `Access-Control-Allow-Origin`이라는 값에 “이 리소스를 접근하는 것이 허용된 출처”를 내려주고, 이후 응답을 받은 브라우저는 자신이 보냈던 요청의 `Origin`과 서버가 보내준 응답의 `Access-Control-Allow-Origin`을 비교해본 후 이 응답이 유효한 응답인지 아닌지를 결정한다.

## Spring Web MVC에서 CORS

### HttpServletResponse

> [HttpServletResponse](https://javaee.github.io/javaee-spec/javadocs/javax/servlet/http/HttpServletResponse.html)

```java
@GetMapping
public List<PostDto> list(
	HttpServletResponse response
) {
	response.addHeader("Access-Control-Allow-Origin", "<http://localhost:3000>");
```

Origin과 무관하게 모든 요청을 허용할 거면 그냥 “\*”로 잡아주면 된다.

```java
response.addHeader("Access-Control-Allow-Origin", "*");
```

### @CrossOrigin

> [CrossOrigin](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/CrossOrigin.html)

Spring Web에선 @CrossOrigin 애너테이션을 써주면 된다. 클래스, 메서드 모두 지정 가능.

```java
@CrossOrigin("<http://localhost:3000>")
```

모든 요청을 허용할 거라면 마찬가지로 “\*”로 잡아주면 된다.

```java
@CrossOrigin("*")
```

아무 것도 안 써도 동일함.

```java
@CrossOrigin
```

### WebMvcConfigurer

WebMvcConfigurer 인터페이스에 대한 Spring Bean으로 환경 설정.

```java
@Bean
public WebMvcConfigurer webMvcConfigurer() {
    return new WebMvcConfigurer() {
		
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
        .allowedMethods("GET", "POST", "PATCH", "DELETE", "OPTIONS")
        .allowedOrigins("<http://localhost:3000>");
    }
  };
}
```

마찬가지로 “\*”을 쓰거나 allowedOrigins 메서드를 따로 써주지 않으면 모든 요청을 허용할 수 있다.
