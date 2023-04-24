# Relationship Mapping

데이터 모델 Entity의 관계를 객체 참조로 간단히 활용할 수 있게 해준다.

```java
class Person{
    private Item item;    //Person이 item을 참조
    ...
}
```

CascadeType.ALL + orphanRemoval=true를 통해 DDD의 Aggregate를 구현한다.

처음에는함께 사용하는 걸 권장한다.



객체의 경계 & 불변식을 다룬다. -> Command -> JPA Entity + Relationship Mapping 이용

조희 성능이 중요할 땐 Relational 접근이 필요 -> Query -> JdbcTemplate or SQL Mapper 이용

Command Query 둘을 분리하라!

아래는 Person이 Item 컬렉션을 참조하는 식으로 테이블을 관리하는 방식이다.

객체-관계 불일치 문제를 해결하기 위한 방법.

```java
@Entity
@Table(name = "people")
public class Person {
    
    ...

    @OneToMany(cascade = CascadeType.ALL, orphanRemoval = true)
    @JoinColumn(name = "person_name")
    @OrderBy("name")
    private List<Item> items = new ArrayList<>();

    public void addItem(String name, String usage) {
        Item item = new Item(name, usage, this.name);

        items.add(item);
    }

    public void removeItem(String name) {
        Optional<Item> item = items.stream()
                .filter(i -> i.name().equals(name))
                .findFirst();

        if (item.isEmpty()) {
            return;
        }

        items.remove(item.get());
    }

    public List<Item> items() {
        //원본 넘겨주지않는다. 외부에서 원본 변경 될 수 있음
        return new ArrayList<>(items);
    }
}
```
