## JDBC

- DBMS의 종류와 관계없이 데이터베이스를 조작하기 위한 Java API이다.

### JDBC의 연동과정

- DB연동에 필요한 Connection을 구한다.
- 쿼리를 실행하기 위한 PreparedStatement를 생성한다.
- 쿼리를 실행한 뒤, finally 블록에서 ResultSet, PreparedStatement, Connection을 닫는다.

```apl
Connection을 구하는 부분과 ResultSet,PreparedStatement, Connection을 닫는 부분이 구조적으로 반복된다.
```

# Spring JDBC

- Spring JDBC가 제공하는 클래스 중 JdbcTemplate는 JDBC의 모든 기능을 최대한 활용할 수 있는 유연성을 제공하는 클래스 이다.

- 스프링을 사용하면 Datasource나 Connection, Statement, ResultSet을 직접 사용하지 않고, JdbcTemplate를 사용해서 편리하게 쿼리를 실행 할 수 있다.

- 지금까지 주로 @Autowired를 통해서 JdbcTemplate를 주입받아서 사용해 왔다. 그러나 아래와 같이 직접 만들어 줄 수 있다. 만들 때에는 DataSource가 필요하다.



```java
@Bean(destroyMethod="close")
public DataSoruce dataSource() {
  DataSource ds = new DataSource();
  ds.setDriverClassName("com.mysql.jdbc.Driver");
  ds.setUrl("jdbc:mysql://localhsot/spring5fs?characterEncoding=utf8");
  ds.setUsername("spring5");
  ds.setPassword("spring5");
  ds.setInitializeSize(2);
  ds.setMaxActive(10);
  ds.setTestWhileIdle(true);
  ds.setMinEvictableIdleTimeMillis(1000 * 60 * 3);
}
```



```java
public class MemberDao {
	private JdbcTemplate jdbcTemplate;
	
	public MemberDao(DataSource dataSource) {
		this.jdbcTemplate = new JdbcTemplate(dataSource);
	}
}
```



### JdbcTemplate를 이용한 조회 쿼리

- Query 메소드를 사용한다.
- sql 파라미터로 전달받은 쿼리를 실행하고, RowMapper를 이용해서 ResultSet의 결과를 자바 객체로 변환한다.

```java
List<T> query(String sql, RowMapper<T> rowMapper)
List<T> query(String sql, Object[] args, RowMapper<T> rowMapper)
List<T> query(String sql, RowMapper<T> rowMapper, Object args)
```



- RowMapper 인터페이스

```java
public interface RowMapper<T> {
	T mapRow(ResultSet rs, int rowNum) throws SQLException;
}
```



- 람다식을 사용하여 RowMapper를 구현하여 바로 전달할 수 있다.

```java
public Customer findCustomerById(Long id) {
	String sql = "selct id, first_name, last_name from customers where id = ?";
	Customer customer = this.jdbcTemplate.queryForObject(sql, ((resultSet, rowNum) -> 
                                                       new Customer(rs.getString("first_name"),
                                                                    rs.getString("last_name"))), id);
	return customer
}
```



- 계속 사용되는 RowMapper일 경우 변수로 빼는 것도 가능하다.

```java
private final RowMapper<Customer> actorRowMapper = (resultSet, rowNum) -> {
        Customer customer = new Customer(
                resultSet.getLong("id"),
                resultSet.getString("first_name"),
                resultSet.getString("last_name")
        );
        return customer;
    };
```



- queryForObject() 메서드 사용하기

```java
T queryForObject(String sql, Class<T> requiredType)
T queryForObject(String sql, Class<T> requiredType, Object args)
T queryForObject(String sql, RowMapper<T> rowMapper)
T queryForObject(String sql, RowMapper<T> rowMapper, Object... args)
```



### JdbcTemplate를 이용한 변경 쿼리 실행

- INSERT, UPDATE, DELETE 쿼리는 update() 메서드를 사용한다.

```java
int update(String sql)
int update(String sql, Obejct... args)
```



- PreparedStatementCreator를 이용한 쿼리 실행

```java
public interface PreparedStatementCreator {
	PreparedStatement createPreparedStatement(Connection con) throws SQLException;
}
```



```java
List<T> query(PreparedStatementCreator psc, RowMapper<T> rowMapper)
int update(PreparedStatementCreator psc)
int update(PreparedStatementCreator psc, KeyHolder generatedKeyHolder)
```



### INSERT 쿼리 실행 시 KeyHolder를 이용해서 자동 생성 키 값 구하기

```java
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(new PreparedStatementCreator() {...}, keyHolder);
```



# 트랙잭션

- 두 개 이상의 쿼리를 한 작업으로 실행해야 할 때 사용
- 여러 쿼리를 논리적으로 하나의 작업으로 묶어줌



- rollbackFor
- noRollbackFor



- 