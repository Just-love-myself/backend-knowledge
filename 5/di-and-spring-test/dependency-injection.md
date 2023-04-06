# Dependency Injection

## Factory

객체를 직접 만들지 않고, 객체를 생성하는 책임만 가진 객체를 만들어서 쓴다(SRP).

PostDAO 또는 PostRepository의 구현이 여럿이라, 유용하게 쓸 수 있다.

객체를 모두 팩토리에서 얻는다면, 객체를 관리함으로써 해당 객체를 싱글톤처럼 쓸 수도 있다.

### Factory 객체 사용 전

```java
public class PostService {

    private final PostRepository postRepository;

    public PostService() {

        this.postRepository = new PostRepository();
    }
    
}
```

```java
public class GetPostListService {

    private final PostRepository postRepository;

    public GetPostListService(){
        this.postRepository = new PostRepository();
    }
    
}
```

Post과 관련된 Service가 여러 개인 상황이다. PostRepository를 사용하고 있는데, new 연산자를 통해 매번 객체를 생성하니, 의도치않은 오류를 맞닥뜨릴 수 있다.&#x20;

PostService에서 분명 저장했는데, GetPostListService에서 가져오려고 보니 존재하지 않는다고 뜨는 예처럼 말이다.

Factory 객체를 사용하면 PostRepository를 **싱글톤**처럼 관리할 수 있다.

```java
public class Factory{
    private static PostRepository postRepository;
    
    public static PostRepository postRepository(){
        if(postRepository == null){
            return new PostRepository();
        }
        return postRepository;
    }
}
```

## 싱글톤 패턴

앞서 싱글톤이란 단어가 나왔다. 싱글톤 패턴이란 무엇인지 알아보자.

싱글톤(Singleton) 패턴의 정의는 단순하다. **객체의 인스턴스가 오직 1개만 생성**되는 패턴을 의미한다.

#### 싱글톤 패턴 이점

* 가장 먼저 떠올릴 수 있는 이점은 아무래도 **메모리 측면**일 것이다. 최초 한번의 new 연산자를 통해서 고정된 메모리 영역을 사용하기 때문에 추후 해당 객체에 접근할 때 메모리 낭비를 방지할 수 있다. \
  뿐만 아니라 이미 생성된 인스턴스를 활용하니 속도 측면에서도 이점이 있다고 볼 수 있다.
* 또다른 이점은 다른 클래스 간에 **데이터 공유가 쉽다**는 것이다. \
  싱글톤 인스턴스가 전역으로 사용되는 인스턴스이기 때문에 다른 클래스의 인스턴스들이 접근하여 사용할 수 있다.&#x20;

하지만 여러 클래스의 인스턴스에서 싱글톤 인스턴스의 데이터에 동시에 접근하게 되면 **동시성 문제**가 \
발생할 수 있으니 이점을 유의해서 설계하는 것이 좋다.

#### 싱글턴 패턴 문제점 <a href="#3" id="3"></a>

> 1. 싱글턴 패턴을 구현하는 코드 자체가 많다.
> 2. 의존관계상 클라이언트가 구체 클래스에 의존한다.
> 3. 테스트하기 어렵다.
> 4. 내부 속성을 변경하거나 초기화 하기 어렵다.
> 5. private 생성자로 자식 클래스를 만들기 어렵다.
> 6. 싱글톤 컨테이너
> 7. 스프링에서 위에 단점들을 모두 해결해준다.

즉, 싱글턴 패턴을 사용하게 되면 유연성이 떨어지게 된다는 것이다.

**스프링 컨테이너**는 **싱글톤 패턴을 적용하지 않아도 객체 인스턴스를 싱글톤으로 관리**한다. \
이러한 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하고 객체를 싱글톤으로 유지할 수 있다.

#### **싱글턴 방식의 주의점**&#x20;

> 싱글톤 패턴이든, 스프링에서 객체 인스턴스를 하나만 생성해서 공유하는 상황에서 **객체 인스턴스를 공유하기 때문에 객체 상태를 유지(stateful)하게 설계하면 안된다.**\
> 1\. price는 공유되는 필드이기 때문에 특정 클라이언트가 값을 변경한다.\
> 2\. 실무에서 이런 경우를 종종 보는데, 이로인해 정말 해결하기 어려운 큰 문제들이 터진다.(몇년에 한번씩 꼭 만난다.)

<figure><img src="https://images.velog.io/images/jaeeunxo1/post/391b710a-1a40-4d89-9043-804a0f44f33e/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://images.velog.io/images/jaeeunxo1/post/d36c8b3a-c0a2-45cd-ac62-31e54a47e77e/image.png" alt=""><figcaption></figcaption></figure>

## IoC 그리고 DI

Inversion Of Control : 제어의 역전이라고 부른다.

우리가 프레임워크 없이 개발할 때에는 객체의 생성, 설정, 초기화, 메소드 호출, 소멸(이하 객체의 생명주기)을 프로그래머가 직접 관리한다.&#x20;

또한 전통적인 프로그래밍에서는 외부 라이브러리를 사용할 때, 개발자가 직접 외부 라이브러리를 호출하는 형태로 이용한다.

하지만, 프레임워크를 사용하면 객체의 생명 주기를 모두 프레임워크에 위임할 수 있다. 즉, 외부 라이브러리가 프로그래머가 작성한 코드를 호출하고, 흐름을 제어한다.

프레임워크가 쉽게말해 객체의 생성과 그 객체들의 관리까지 모두 관리한다는 개념

IoC는 DI(Dependency Injection)과 밀접한 관련이 있다. DI는 IoC 원칙을 실현하기 위한 여러 디자인패턴 중 하나이다.

참고 링크 : [https://steady-coding.tistory.com/458](https://steady-coding.tistory.com/458)

### 스프링 프레임워크

> 스프링 컨테이너가 필요에 따라 개발자 대신 Bean들을 관리(제어)해주는 행위

일반적인 상황에서는 개발자가 직접 객체를 제어해야 했다. new 연산자를 통해 객체를 생성하고, 객체의 의존성을 맺어주고, 초기화를 해주고 등등...

하지만 Spring 에서는 xml파일 또는 어노테이션 방식으로 스프링 컨테이너에 Bean(객체)를 등록하기만 하면, 스프링 컨테이너에서 Bean의 생명주기(생성 -> 의존성 설정 -> 초기화 -> 소멸)를 전부 관리해준다.

즉, **객체에 대한 제어권이 컨테이너로 역전**되기 때문에 제어의 역전이라고 하는 것이다.

## 스프링 컨테이너

개발자 대신 객체들을 관리해주기 때문에 **IoC 컨테이너**라고도 불리고, 의존관계 주입에 초점을 맞춰서 **DI 컨테이너**라고도 한다.&#x20;

스프링에선 빈을 관리하는 컨테이너를 **스프링 컨테이너**라고 부른다.

spring이 관리하는 빈을 콕 짚어서 **Spring Bean**이라고 얘기한다. (Java Bean != Spring Bean)

스프링 컨테이너를 구현하는 방법은 두 가지가 있다. -> BeanFactory, ApplicationContext

<figure><img src="../../.gitbook/assets/container.jpg" alt=""><figcaption><p>Spring Container</p></figcaption></figure>

#### BeanFactory

* 스프링 컨테이너의 최상위 인터페이스다. (최소한의 IoC 컨테이너)
* 스프링 빈을 관리하고 조회하는 역할을 담당한다.
* getBean()을 제공한다.
* 지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능이다.&#x20;

1. BeanDefinition을 이용해서 Bean 정보를 설정한다.
2. BeanFactory를 이용해서 해당 Bean을 Factory에 등록한다.
3. getBean() 메서드를 통해 빈을 가져올 수 있다.

```java
@Test
@DisplayName("Spring IoC Container를 통해 PostController 객체 얻기 테스트")
void getPostController() {
    ObjectMapper objectMapper = new ObjectMapper();

    //BeanFactory 객체 생성
    DefaultListableBeanFactory beanFactory = new DefaultListableBeanFactory();

    //빈 생성할 때, 생성자를 통해 객체 넘겨준다. (=DI)
    ConstructorArgumentValues constructorArgs = new ConstructorArgumentValues();
    constructorArgs.addIndexedArgumentValue(0, objectMapper);

    //빈 멤버 필드 값 설정
    MutablePropertyValues propertyValues = new MutablePropertyValues();
    propertyValues.add("objectMapper", objectMapper);
    
    //BeanDefinition 객체 생성 -> Bean 정보 설정
    GenericBeanDefinition beanDefinition = new GenericBeanDefinition();
    beanDefinition.setConstructorArgumentValues(constructorArgs);
    beanDefinition.setPropertyValues(propertyValues);
    beanDefinition.setBeanClass(PostController.class);
    
    //beanFactory에 빈 등록
    beanFactory.registerBeanDefinition("postController", beanDefinition);
    
    //getBean()으로 빈 불러오기
    PostController postController = 
        beanFactory.getBean("postController", PostController.class);
}
```

#### ApplicationContext

* BeanFactory 기능을 모두 상속받아서 제공한다.
* 빈을 관리하고 검색하는 기능을 BeanFactory 가 제공해주는데, 둘의 차이는?
* 애플리케이션을 개발할 때는 빈은 관리하고 조회하는 기능은 물론이고, 수많은 부가기능이 필요하다.
* MessageSource 메시지 소스를 활용한 국제화 기능 -> 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로.
* EnvironmentCapable 환경 변수 -> 로컬, 개발, 운영등을 구분해서 처리
* ApplicationEventPublisher  애플리케이션 이벤트 -> 이벤트를 발행하고 구독하는 모델을 편리하게 지원
* ResourceLoader  편리한 리소스 조회 -> 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

```java
@Configuration
public class DemoApplication {
    public static void main(String[] args) {
	ApplicationContext context = 
		new AnnotationConfigApplicationContext(DemoApplication.class);
		
	System.out.println("-".repeat(80));
		
	PostController controller = 
		context.getBean("postController", PostController.class);
	System.out.println(controller);
	
	ObjectMapper objectMapper = 
		context.getBean("objectMapper", ObjectMapper.class);
	System.out.println(objectMapper);
    }
	
    @Bean
    public PostController postController() {
 	System.out.println("Create bean: postController");
	return new PostController(objectMapper());
    }
	
    @Bean
    public ObjectMapper objectMapper() {
	System.out.println("Create bean: objectMapper");
	return new ObjectMapper();
    }
}
```

스프링 애플리케이션 대부분은 최소한의 IoC Container인 BeanFactory를 넘어서, **ApplicationContext**를 사용할 때가 많다.&#x20;

예전에는 Bean에 대한 정보를 XML 파일로 써줬는데, 최근에는 Java의 Annotation으로 처리한다.&#x20;

Bean은 Java Config에서 @Bean 애너테이션을 써서 정의하거나, 해당 클래스에 @Component 애너테이션을 붙여주고 Scan한다(@ComponentScan 사용)

빈 수동 등록 -> @Configuration -> @Bean으로 등록

빈 자동 등록 -> @Component (@Controller, @Service, @Repository는 모두 @Component다.)











