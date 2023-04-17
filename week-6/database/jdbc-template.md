# JDBC Template

## Spring JdbcTemplate

* JdbcTemplate 의존주입 해준다.

```java
@Component
public class AppRunner implements CommandLineRunner {
	private final JdbcTemplate jdbcTemplate;

	public AppRunner(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	@Override
	public void run(String... args) throws Exception {
		String sql = "SELECT name FROM people";
		
		jdbcTemplate.query(sql, resultSet -> {
			while (resultSet.next()) {
				String name = resultSet.getString("name");
				System.out.println(name);
			}
		});
	}
}
```

이렇게 실행하면, 오류난다. creating bean with name 'dataSource' Error가 뜬다.

따라서 application.properties에 입력해줘야함.

가독성 편리성을 위해 application.yml 형식으로 자주 씀

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/postgres
    username: postgres
    password: password
```

* PreparedStatement를 좀 더 간편하게 쓸 수 있다.
  * ex) 문자열 포함하는지 확인할 때 LIKE와 % 사용가능
  * query() 세 번째 인자로 넣어준다.

```java
String sql = "SELECT name FROM people WHERE name LIKE ?";

jdbcTemplate.query(sql, resultSet -> {
	String name = resultSet.getString("name");

	System.out.println(name);
}, "%우");
```

* Query(SELECT)가 아니라 Command(INSERT, UPDATE, DELETE)를 실행할 때는 update 메서드 사용.

```java
String sql = """
		INSERT INTO people (name, age, gender) VALUES (?, ?, ?)
		""";
			
jdbcTemplate.update(sql, "홍길동", 15, "남");
```

* TransactionTemplate을 써서 트랜잭션을 관리할 수 있다.
* 트랜잭션이란 여러 sql문이 있을 때, 모두 이상 없으면 Commit, 하나라도 에러 있으면 rollback시킨다.
  * 예외가 중간에 발생하면 그냥 트랜잭션 범위에 해당하는 것들은 모두 rollback시킨다.
  * callback 함수 처럼 쓴다.
* execute(status ->{}) : 중괄호 안이트랜잭션 범위

```java
@Component
public class AppRunner implements CommandLineRunner {
	private JdbcTemplate jdbcTemplate;
	private TransactionTemplate transactionTemplate;
	
	public AppRunner(JdbcTemplate jdbcTemplate,
				TransactionTemplate transactionTemplate) {
		this.jdbcTemplate = jdbcTemplate;
		this.transactionTemplate = transactionTemplate;
	}
	
	@Override
	public void run(String... args) throws Exception {
		transactionTemplate.execute(status -> {
			String sql = """
				INSERT INTO people(name, age, gender) VALUES(?, ?, ?)
				""";
	
			jdbcTemplate.update(sql, "홍길동", 15, "남");
	
			// 이런 식으로 예외를 던지면 전부 없던 일로 만들 수 있다.
			// throw new RuntimeException("FAIL!");
	
			return null;
		});
	}
}
```

* <mark style="color:red;">트랜잭션의 범위는 Application Layer가 트랜잭션 경계가 되는게 좋다.</mark>
  * <mark style="color:red;">즉 Service 단에서 트랜잭션 범위를 잡아보자!!</mark>
