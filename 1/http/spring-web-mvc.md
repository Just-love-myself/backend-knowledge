---
description: 앞서 만들었던 http서버를 spring을 이용해서 훨씬 편리하게 만들어보자
---

# Spring Web MVC

### Spring Initializer

이전에는 만들고자 하는 디렉터리위치에서  **gradle init**을 통해서 프로젝트를 만들었다.

Spring Initializer은 이와 같은 역할 + spring dependecy 설정도 같이 추가할 수 있다.

start.spring.io 사이트에서 프로젝트 만들면 된다.

### Web Server와 Web Application Server(WAS)

스프링 부트 웹 애플리케이션을 실행하면 내장 서블릿 컨테이너인 웹서버 톰캣을 사용해서 애플리케이션이 구동된다.

이 톰캣은 "아파티 톰캣"이란 이름을 갖고 있다.

여기서 아파치는 무엇이고 톰캣은 무엇인지 그리고 웹 서버의 역할은 무엇인지 알아보자.

**Aapach**

흔히 부르는 아파치는 Aapache HTTP Web Server를 의미한다.

http 웹 서버는 http 요청을 처리할 수 있는 웹 서버이고, 아파치 http 웹 서버는 아파치에서 만든 http 요청을 처리하는 웹 서버인 것이다.&#x20;

현대 네트워크는 TCP/IP 프로토콜 기반에서 http 통신이 이뤄지므로 http를 생략하고 web server라고 부르기도 한다.

따라서 아파치 == 웹 서버라 할 수 있다.

**Tomcat**

이 톰캣을 흔히 WAS라고 한다. WAS는 웹 서버와 웹 컨테이너가 결합된 구조로 다양한 기능을 컨테이너에 구현하여 다양한 역할을 수행하는 서버를 말한다.

웹 서버는 이러한 웹 컨테이너가 없으므로 당연히 동적 요청을 처리할 수 없다.

<figure><img src="../../.gitbook/assets/WAS 구조.png" alt=""><figcaption></figcaption></figure>

**웹 서버** : 정적인 HTML, 이미지를 제공하는 서버다.

**WAS** : 웹 서버 역할 + 동적인 처리를 담당하는 서버다.

WAS의 Web Container(**Servlet Container**)가 클라이언트로부터 http 요청을 받아 Servlet을 동작하게 한다.

**Servlet**이란 비즈니스 로직이라고 생각하면 편한다. \
스프링 부트에서 @RestController + @PostMapping과 같은 애노테이션을 특정 클래스에 명시하고 해당 클래스에서 비즈니스 로직을 정의하는데 이 때 작성하는 비즈니스 로직이라고 생각하면 된다.\
\-> 로직을 통해 동적 data를 처리하는 것

위에서 WAS가 웹 서버의 역할도 할 수 있다고 했는데, \
그렇다면 웹 서버의 기능까지 모두 수행하면 안 되는 걸까?

**웹 서버와 WAS를 분리해야 한다**&#x20;

서버 부하 방지, 효율적인 분산 처리,  보안 강화 등의 이유로 분리한다고 한다.

### MVC 아키텍처 패턴

[https://medium.com/@jang.wangsu/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-mvc-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-1d74fac6e256](https://medium.com/@jang.wangsu/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4-mvc-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-1d74fac6e256) - MVC 아키텍처

**MVC**는 Model-View-Controller 의 약자다.

개발 할 때, 3가지 형태로 역할을 나누어 개발하는 방법론.

비지니스 처리 로직(Model)과 사용자 인터페이스 요소(View)들을 <mark style="background-color:orange;">**분리시켜**</mark> \
서로 영향없이 개발 하기 수월하다는 장점이 있다. -> <mark style="color:orange;">**관심사의 분리(Seperation of Concern)**</mark>

* **Model**은 어플리케이션이 “**무엇**”을 할 것인지를 정의. 내부 비지니스 로직을 처리하기 위한 역할
  * 처리되는 알고리즘, DB 와 상호작용(**CRUD** Create Read Update Delete), 데이터 등등..
* **Controller**는 모델이 “**어떻게**” 처리할 지를 알려주는 역할. 화면에서 사용자의 요청을 받아서 처리되는 부분을 구현되게 되며, **요청 내용을 분석**해서 Model과 View에 업데이트 요청\
  그리고 Controller는 Model과 View가 각각 무엇을 해야 할 지를 알고 있고, **통제**한다. &#x20;
* **View**는 화면에 “**무엇”** 인가를 **보여주기 위한 역할**.&#x20;

### Spring MVC

[https://engkimbs.tistory.com/686](https://engkimbs.tistory.com/686) - Spring MVC

웹 프로그래밍을 구축할 때의 설계 모델은 Model1, Model2 크게 2가지가 있다.

* **Model 1**은 아키텍처가 간단하고 JSP에 거의 모든 로직을 집어넣기 때문에 작은 웹 어플리케이션을 제작할 때는 큰 무리가 없지만 대규모 웹 어플리케이션을 제작하게 될 시 유지보수에 큰 어려움이 따른다.
* **Model 2**는 이 Model 1방식을 보완한 아키텍처다. MVC 패턴에 맡게 Model, Controller, View 부분로 모듈화됬고 JSP는 로직 처리가 없이 단순히 Client에게 보여지는 뷰만을 담당한다.
* 이 방식은 각각 모듈화되어 있어 유지보수가 매우 쉬워지는 큰 장점이 있다. 현재의 웹어플리케이션은 거의 Model 2 방식을 따른다 보면 된다.

**스프링 MVC**는 Model 2 방식을 따르며 이 Model 2의 아키텍처에 맞게 설계되어 있다.

<figure><img src="../../.gitbook/assets/스프링 MVC.jpg" alt=""><figcaption><p>Spring MVC 모델</p></figcaption></figure>

1. DispatcherServlet가 Client요청을 받음 (중앙 제어실과 같음)
2. HandlerMapping이 알맞은 Controller를 찾음
3. HandlerMapping에 실행할 Controller의 메서드를 찾음&#x20;
4. Controller의 메서드를 실행하며 그 결과 Model로서 DispatcherServlet에 반환
5. ViewResolver는 알맞은 JSP파일을 찾음
6. View는 JSP파일을 Model의 정보를 토대로 Client에게 반환

### Annotation

Annotation은 클래스와 메서드에 추가하여 다양한 기능을 부여하는 역할을 한다.

자바 소스 코드에 추가하여 사용할 수 있는 메타데이터의 일종이다.

어노테이션에도 종류가 있다.

* 표준(내장) 어노테이션 : 자바가 기본적으로 제공해주는 어노테이션
* 메타 어노테이션 : 어노테이션을 위한 어노테이션
* 사용자정의 어노테이션 : 사용자가 직접 정의하는 어노테이션

### Spring Annotation

**@RestController** : @Controller + @ResponseBody가 합쳐진 것.

@Controller만 붙어있다면, "/hello"라는 path로 요청이 들어왔을 때,&#x20;

1. 해당 path를 처리하는 controller 코드는 아래와 같을 것이다.

```java
@Controller
public class HelloController{
    
    @GetMapping("/hello")
    public String hello() {
        return "hello";
    }
}
```

2. 리턴된 문자열 "hello"는 **viewResolver**가 받아, resources/static에서 hello.html을 찾는다.

@ResponseBody를 사용하면

* HTTP의 BODY에 문자 내용(String)을 직접 반환한다.
* viewResolver 대신에 HttpMessageConverter가 동작
* 기본 문자처리 : StringHttpMessageConverter
* 기본 객체처리 : MappingJackson2HttpMessageConverter\
  (객체를 반환하면 객체가 JSON으로 변환되어 응답된다.)



**@GetMapping**

특정 url로부터 요청을 받으면 어떤 Controller에서 처리할 지 알아야 한다.\
이 때, 특정 url을 요청을 수행할 Controller과 매핑하여 지정하는 어노테이션이 **@RequestMapping**이다.

@RequestMapping(method = RequestMethod.GET, ...)의 의미와 같다.

HTTP 메서드 중 GET으로 오는 요청을 처리하는 애노테이션이다.





