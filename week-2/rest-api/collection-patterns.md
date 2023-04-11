---
description: 리소스를 중심으로 구성하는 API 디자인에 대해 알아보자
---

# Collection Patterns

## Colletion Patterns

**Collection Pattern**을 쓰는 경우:

* `/posts` ⇒ 모든 게시물을 하나의 URI로 표현(게시물 목록으로도 활용 가능). 일반적으로 **복수형**을 사용한다.
* `/posts/the-first`
* `/posts/test`
* `/posts/{id}` 또는 `/posts/:id` 등으로 일반적인 형태로 쓸 수 있다. 여기서 {id}를 {post\_id}나 {postId} 등으로 표기할 수도 있다. 여기서는 그냥 Post ID라고 부를 예정.

Post ID는 리소스의 ID가 아님에 주의!

* Resource ID = URI = URL\
  리소스의 식별자는 URI이다.
* Post ID = Resource ID를 구성하는 요소 중 하나. posts 그룹 내 식별자(ID)

**특정 게시물에 댓글이 달리는 상황**

* `/posts/{post_id}/comments` ⇒ Collection
  * `/comments?post_id={post_id}` 형태로 써줘도 된다. 이렇게 쓰면 Post ID를 이용해 필터링한다는 의미가 됨. URI에서 드러나는 의미는 미묘하게 다르지만, 구현하는 코드나 결과는 사실상 동일하다.
* `/posts/{post_id}/comments/{id}` ⇒ Element. 여기서 {id}는 {comment\_id}를 의미함.
  * `/comments/{id}` 형태로 간단히 구성해도 된다. 이렇게 할 경우, URI만 봐서는 어떤 게시물에 대한 댓글인지 알 수 없다. 댓글 삭제 등 간단한 작업 요청 상황이라면 오히려 좋을 수도 있다.

**특정 게시물을 고치는 페이지를 표현**하고 싶다면, 마찬가지로 구성할 수 있다.

* `/posts/{id}/edit` ⇒ “Edit Page”라는 리소스.
  * 마찬가지로 `/edit?post_id={post_id}` 형태로 써줄 수도 있다. 다만, `/comments/{id}/edit` 같은 게 도입될 경우 `/edit?comment_id={comment_id}` 같은 것과 구분해서 처리하는 게 복잡할 수 있다.
  * REST API의 경우, 페이지만 표현할 일은 거의 없다. 이런 표현은 F/E에 맡기고, 그냥 `/items`, `/items/{id}` 같은 URL만 쓰도록 제약해도 무방하다.

**그룹이 아닌 경우라면 그냥 단수형을 써도 된다.**

* `/session` ⇒ 세션은 하나만 유지된다. 다른 세션을 참고할 일도 없다. `/sessions/1` 같은 접속이 있으면 안 된다.
* `/edit?blahblah` ⇒ 위에서 언급한 케이스.
  * 만약 편집 페이지의 유형을 Edit ID 따위로 표현하고 싶다면, `/edits/post`, `/edits/comment` 같이 컬렉션 패턴과 복수형을 사용할 수 있다.
  * 하지만 이렇게 하고 싶다면 edit보다는 그냥 page란 리소스를 사용하는 걸 추천. Page란 리소스, 그리고 Page ID란 모양이 훨씬 명확하다. 이렇게 하면 `/pages/edit-post`, `/pages/edit-comment` 같이 훨씬 자연스럽게 표현할 수 있게 된다.



## CQS (Command Query Seperation)

모든 메서드는 한 번의 액션에서 **상태를 변경하는 커맨드**든 **데이터를 반환하는 쿼리**든 한 가지 액션만 취해야한다는 것이다.

데이터 변경 관련 문제가 발생했을 때, 변경이 일어나는 메서드만 확인해보면 된다.

따라서 유지보수가 더 편리해진다.

그런데 예외사항(trade-off)도 있다.

* CQS의 대표적 예외사항으론 Stack의 pop()이다.
* stack pop()은 가장 최근에 push된 값을 반환하고, stack에서 제거하는 메서드이다.
* pop()은 command와 query가 합쳐진 것이다.
* 할 수 있는 한 CQS 원칙을 지키되, stack의 pop과 같이 매우 유용한 상황에선 CQS 원칙을 깨뜨릴 준비를 해야한다.

#### JPA로 보는 예제 &#x20;

* **insert**
  * insert는 DB 상태 변화를 일으키지만 id를 반환한다. \
    이는 필요에 의해 CQS 원칙을 깬 것으로 볼 수 있다.
  * <mark style="background-color:red;">질문 : stack의 pop()처럼 CQS원칙을 깰 정도로 유용한 상황인가?</mark>
  * <mark style="background-color:red;">-> 당연하다. id를 반환하지 않으면 이후에 조회할 방법이 없음</mark>

```java
private EntityManager em;

    @Transactional
    public Long save(Item item){
        em.persist(item);
        Item item = em.find(Item.class, id);
        return item.getId();
    }
```

* **update**
  * 반환 X
  * DB 데이터는 변경하지만, 아무 것도 반환하지 않는다. -> command

```java
private EntityManager em;

    @Transactional
    public User update(User user){
        em.persist(user)
    }
```

* **select**
  * DB 데이터를 변경하지 않고, 결과 값만 반환 -> query

```java
private EntityManager em;

    @Transactional
    public User findOne(Long id){
        User findUser = em.find(User.class, id);
        return findUser;
    }
```
