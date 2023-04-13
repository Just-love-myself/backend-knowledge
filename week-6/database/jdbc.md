# JDBC

## JDBC

* JDBC란 Java에서 RDBMS를 사용할 수 있게 해주는 API.
* API이기 때문에, DBMS 종류에 따라 제공하는 JDBC Driver가 있어야 사용할 수 있다.
  * Driver에서 Connection을 가져와야한다.

### Connection

```java
String url = "jdbc:postgresql://localhost:5432/postgres";

Properties properties = new Properties();
properties.put("user", "postgres");
properties.put("password", "password");

Connection connection = DriverManager.getConnection(url, properties);
```

“No suitable driver found for jdbc:postgresql://localhost:5432/postgres” 에러

→ PostgreSQL용 JDBC Driver가 필요함. -> build.gradle에 의존성 추가

```
implementation 'org.postgresql:postgresql:42.5.4'
```

* 이후 Statement로 sql문을 쓰는 방법이 있고
* PreparedStatement로 보다 발전된 방법이 있다.
  * SQL Injection 공격에 좋다.
  * SQL문을 작성하는데, 값을 ?로 써놓고 나중에 preparedStatement.setInt() 등으로 조립한다.

### Statement

```java
//Connection에서 가져온다
Statement statement = connection.createStatement();	

String query = "SELECT * FROM people";

//SQL문 결과 값(tuple)이 ResultSet에 담긴다.
ResultSet resultSet = statement.executeQuery(query);

while (resultSet.next()) {
	String name = resultSet.getString("name");
	
	System.out.println(name);
}
```
