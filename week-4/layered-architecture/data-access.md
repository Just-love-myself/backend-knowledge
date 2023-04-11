# Data Access

## DAO 등장 전

애플리케이션을 두 가지로 나눠보면 아래와 같다.

* 웹 - UI Layer -> Controller로 구현
* 기능(Business Logic) - Application Layer -> service로 구현

게시물 관련 처리 애플리케이션은 PostController - PostService 두 가지 구조로 애플리케이션을 **분리**할 수 있다.

그러다보면 PostService의 메서드들(CRUD) 안에 Data 관련 로직까지 담기게 된다.

따라서 Data 관련 변경사항이 있을 때 PostService 코드까지 모두 변경해야하는 불상사가 발생한다.

이는 결합도가 높은 상황이기에, 좋지 않은 설계라 볼 수 있다.

```java
public class PostService{
    //비즈니스 로직에 data를 담을 컬렉션이 정의되어있다.
    //만약 컬렉션을 변경하고 싶다면?
    //그 땐 코드 전체를 수정해야함 -> 결합도가 크다.
    private final List<PostDTO> postDtos; 
    
    public PostService(){
        this.postDtos = = new ArrayList<>(List.of(
            new PostDTO("1", "title", "content"),
            new PostDTO("2", "title_2", "content_2")
        ));
    }
    
    public List<PostDTO> getPostDtos() {
        return postDtos;
    }
    
    public public PostDTO getPostDto(String id) {
        return postDtos.stream()
                .filter(i -> i.getId().equals(id))
                .findFirst()
                .orElseThrow(PostNotFound::new);
    }
}
```

## DAO 등장

따라서 우린 관심사의 분리를 한 번 더 한다.

그래서 등장한 것이 Data Access Object (DAO) 이다.

따라서데이터 관련 로직은 DAO가 담당하고 Service는 DAO에게 요청만 하면 된다. (결합도를 낮춘다)

### &#x20;List 컬렉션을 사용하는 PostListDAO 코드

```java
public class PostListDAO{
    //Data Access Object

    private final List<PostDTO> postDtos;

    public PostListDAO() {
        postDtos = new ArrayList<>(List.of(
                new PostDTO("1", "제목", "테스트입니다."),
                new PostDTO("2", "2등", "2등이닷!")
        ));
    }

    public List<PostDTO> findAll() {
        //내부 컬렉션(원본)을 그대로 바깥에 주면 좋지않다. 
        //-> 바깥에서 수정이 일어날 수 있음 
        //-> 캡슐화 깨짐
        //따라서 복사본을 새로 만들어서 반환해준다.
        return new ArrayList<>(postDtos);
    }

    public PostDTO find(String id) {
        return postDtos.stream()
                .filter(i -> i.getId().equals(id))
                .findFirst()
                .orElseThrow(PostNotFound::new);
    }
}
```

### 변경된 PostService 코드

Service에선 데이터 관련 책임은 DAO에게 전격으로 맡기고, 자신은 비즈니스 로직에만 집중할 수 있다.

```java
public class PostService {

    private final PostListDAO postListDAO;

    public PostService() {
        this.postListDAO= new PostListDAO();
    }

    public List<PostDTO> getPostDtos() {
        return postListDAO.findAll();
    }

    public PostDTO getPostDto(String id) {
        return postListDAO.find(id);
    }
}
```

## DAO Interface

하지만 여기서 또 문제가 발생한다.&#x20;

리스트를 이용한 데이터 저장은 단점이 있다.

바로, 데이터 양이 많아지면 데이터를 찾는게 오래걸리고 비효율적이다는 것이다.

이는 Map 컬렉션을 이용해서 해결할 수 있는데, 생각해보자

Map 컬렉션을 이용한 DAO 클래스를 만들어도, PostService 코드를 변경해야하는 상황을 야기한다.

아래 코드를 보면 결합도가 굉장히 높은 것을 알 수 있다.

### Interface 등장 전

```java
public class PostService {
    //Map을 이용한 DAO를 사용하곳 싶다면, 필드와 생성자를 모두 바꿔야함.
    //private final PostListDAO postListDAO;
    private final PostMapDAO postMapDAO;
    
    public PostService() {
        //this.postListDAO= new PostListDAO();
        this.postMapDAO = new PostMapDAO();
    }

    public List<PostDTO> getPostDtos() {
        //return postListDAO.findAll();
        return postMapDAO.findAll();
    }

    public PostDTO getPostDto(String id) {
        //return postListDAO.find(id);
        return postMapDAO.find(id);
    }
}
```

### Interface 등장

이는 Interface를 통해서 해결할 수 있다. (=다형성을 이용하자)

PostListDAO, PostMapDAO가 공통적으로 사용하는 메서드(연산)들을 한 곳에 모으는 것이다.

PostDAO 인터페이스를 만들고 operation들의 목록을 정의한 뒤, \
PostListDAO, PostMapDAO 클래스가 이를 상속받아 각 컬렉션에 맞는 데이터 로직을 짜면 된다.

#### PostDAO Interface

```java
public interface PostDAO {

    List<PostDTO> findAll();

    PostDTO find(String id);

    void save(PostDTO postDTO);

    void delete(String id);
}
```

#### PostService

```java
public class PostService {

    //service에선 Data Access 관련 interface를 통해 부탁하는 것이다.
    //(=책임을 할당하는 것 또는 협력)
    private final PostDAO postDAO;

    public PostService() {
        //상황에 맞게 주입해주면 된다.
        //this.postDAO = new PostListDAO(); 
        this.postDAO = new PostMapDAO();
    }

    public List<PostDTO> getPostDtos() {
        return postDAO.findAll();
    }

    public PostDTO getPostDto(String id) {
        return postDAO.find(id);
    }
}
```

Interface를 만들면 PostService는 상황에 맞게 DAO를 선택할 수 있게 된다.

중간에 DAO를 변경하더라도, 생성자 부분만 바꿔주면 되기 때문에 결합도가 이전보다 굉장히 낮아진다.
