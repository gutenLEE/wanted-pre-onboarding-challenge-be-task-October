### 전통적 방식의 트랜색션 동작

전통적인 JDBC 트랜잭션 관리는 크게 3단계의 프로세스로 수행된다.

DB 커넥션 → 비스니스 로직 수행 → 커밋 또는 롤백

```java
import java.sql.Connection;

Connection connection = dataSource.getConnection(); // (1)

try (connection) {
    connection.setAutoCommit(false); // (2)
    // execute some SQL statements...
    connection.commit(); // (3)

} catch (SQLException e) {
    connection.rollback(); // (4)
}
```

### 스프링 부트에서의 트랜잭션 동작

스프링에서는 JDBC 트랜잭션을 시작하고, 커밋, 롤백을 쉽고 편리하게 관리할 수 있게 해준다.

```java
@Service
public class UserService {

    @Autowired
    private TransactionTemplate template;

    public Long registerUser(User user) {
        Long id = template.execute(status ->  {
            // execute some SQL that e.g.
            // inserts the user into the db and returns the autogenerated id
            return id;
        });
    }
}
```

TransactionTemplate 나 PlatformTransactionManager 를 통해 커넥션을 맺고, 종료 그리고 SQLException 예외처리를 위한 try-catch 코드도 필요없이 단지 쿼리 실행 코드만 수행하여 좀 더 비즈니스 로직에 맞는 코드만 있다.

이는 스프링의 핵심 기능인 AOP에 의해 가능하다. `execute` 메서드 호출 전,후로 필요한 코드는 다른 객체가 실행한다.  

<img src="https://www.marcobehler.com/images/document1.png" width="600">