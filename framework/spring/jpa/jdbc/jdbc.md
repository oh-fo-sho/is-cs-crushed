# JDBC (Java Database Connectivity)

### **정의 및 역할**

- JDBC는 자바 애플리케이션과 관계형 데이터베이스 간의 **연결 및 통신을 표준화**하기 제공되는 API
- JDBC는 데이터베이스 독립성을 제공하고 데이터베이스 드라이버를 통해 다양한 RDB와 통신이 가능하다

### **특징**

1. 표준화된 인터페이스
   1. JDBC는 표준 인터페이스를 제공하며 데이터베이스 벤더가 이 표준에 맞는 드라이버를 구현한다
2. SQL 의존성
   1. 개발자가 직접 SQL을 작성해야 하며 데이터 조회, 생성, 수정, 삭제 등의 작업을 코드에서 수동으로 처리해야 한다
3. 반복적이고 복잡함
   1. CRUD 작업에 대해 비슷한 코드가 자주 반복되며 SQL 작성과 결과 처리 로직이 복잡해질 수 있다
4. 성능 한계
   1. 데이터베이스와 매번 새로 연결해야 하므로 연결 비용이 높고 캐싱과 같은 고급 기능들을 직접 구현해야한다

JDBC(Java Database Connectivity)는 Java에서 관계형 데이터베이스와 연결하여 데이터를 처리할 수 있도록 해주는 API이다. JDBC는 Java 애플리케이션과 데이터베이스 간의 상호작용을 관리하며 SQL 쿼리 실행, 결과 처리, 트랜잭션 관리 등을 담당한다

JDBC를 사용하여 데이터베이스와 연결하고 데이터를 조회하거나 수정하는 과정은 일반적으로 다음과 같은 단계로 이루어진다

1. 드라이버 로드 : JDBC 드라이버를 로드하여 데이터베이스와 연결 준비
2. 연결 획득 : 데이터베이스에 연결
3. 쿼리 실행 : SQL 쿼리를 실행하여 데이터를 조회하거나 수정
4. 결과 처리 : 쿼리 결과 처리
5. 연결 종료 : 연결을 닫아 리소스를 해제

## 접속정보 설정하기

`ConnectionUtil` 클래스는 JDBC 연결을 관리하는 유틸리티 클래스이다.

1.  **`MysqlConnectionConstant`와 `H2ConnectionConstant` 클래스**
    이 클래스들은 각각 **MySQL**과 **H2** 데이터베이스에 접속할 때 필요한 상수들을 정의하고 있습니다. 접속 정보는 데이터베이스 URL, 사용자명, 비밀번호로 이루어져 있습니다. - **MySQL** 접속 정보:
    `java
    public static final String URL = "jdbc:mysql://localhost:3307/est_jdbc";
    public static final String USER = "cassidy";
    public static final String PASSWORD = "65";
    `

        - `URL`: `jdbc:mysql://localhost:3307/est_jdbc`는 MySQL 서버가 `localhost`에서 포트 `3307`으로 동작하며, 데이터베이스 이름은 `est_jdbc`임을 나타낸다
            - mariadb가 3306을 쓰고 있어서 mysql은 3307로 사용중입니다
        - `USER`: 데이터베이스 사용자명은 `cassidy`
        - `PASSWORD`: 사용자의 비밀번호는 `65`

**2. `getConnection` 메서드**
이 메서드는 `DriverManager.getConnection()`을 호출하여 데이터베이스에 연결을 생성하고, `Connection` 객체를 반환한다

- `DriverManager.getConnection()`은 접속 정보를 받아 데이터베이스에 연결한다
- `getConnection()` 메서드 내에서는 MySQL 접속 정보를 사용하도록 설정되어 있다

연결에 실패할 경우 `SQLException`이나 일반적인 예외가 발생하면 이를 `RuntimeException`으로 감싸서 처리

<aside>
💡

어느 서버나 클라이언트나 데이터베이스에 접속하려면 접속 정보가 필요하다. 방법에만 차이가 있는데 JDBC만을 사용할 경우 이와 같이, Hibernate를 사용할 거라면 xml에, JPA를 사용할거라면 yml 에 정의하면 된다

</aside>

```java
    @Test
    @DisplayName("Database Connection Test")
    void connection_test() throws Exception {
        Connection connection = ConnectionUtil.getConnection();
        log.info("connection = {}", connection);
        log.info("connection.getClass() = {}", connection.getClass());
    }

    @Test
    @DisplayName("DriverManager 연결 객체 테스트")
    void driver_manager_connect_test() throws Exception {
       Connection connection1 = DriverManager.getConnection(
               ConnectionUtil.MysqlConnectionConstant.URL, ConnectionUtil.MysqlConnectionConstant.USER, ConnectionUtil.MysqlConnectionConstant.PASSWORD
       );

       Connection connection2 = DriverManager.getConnection(
               ConnectionUtil.H2ConnectionConstant.URL, ConnectionUtil.H2ConnectionConstant.USER, ConnectionUtil.H2ConnectionConstant.PASSWORD
       );

        log.info("connection1.getClass() = {}", connection1.getClass());
        log.info("connection2.getClass() = {}", connection2.getClass());
        assertThat(connection1).isNotEqualTo(connection2);
    }
```

이와 같은 연결 테스트로 데이터베이스와 연결이 되었음을 확인할 수 있었다

## JDBC 사용해보기

```java
    Connection conn;
    Statement stmt;
    PreparedStatement pstmt;
    ResultSet rs;

    @BeforeEach
    void setUp() {
        conn = ConnectionUtil.getConnection();
    }

    @AfterEach
    void tearDown() {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                log.error(e.getMessage());
            }
        }
        if (pstmt != null) {
            try {
                pstmt.close();
            } catch (SQLException e) {
                log.error(e.getMessage(), e);
            }
        }
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                log.error(e.getMessage(), e);
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                log.error(e.getMessage(), e);
            }
        }

    }
```

```java
    @Test
    @DisplayName("Jdbc INSERT into Member")
    void jdbc_insert_test() throws Exception {
        // 회원이 회원정보를 입력해서 받았다
        Member admin = getMember("admin", "admin");
        Member user = getMember("user", "user");

        String sql1 = genInsertMemberQuery(admin);
        String sql2 = genInsertMemberQuery(user);

        stmt = conn.createStatement();
        int result1 = stmt.executeUpdate(sql1);
        log.info("result1 = {}", result1);
        int result2 = stmt.executeUpdate(sql2);
        log.info("result2 = {}", result2);
    }
```

### 1. **`@BeforeEach`**

- 테스트 메서드 실행 전에 실행되는 메서드
- 여기서는 데이터베이스 연결을 설정하는 `conn` 객체를 준비한다
- `ConnectionUtil.getConnection()`을 호출해 데이터베이스에 연결한다

### 2. **`@AfterEach`**

- 테스트 메서드 실행 후 실행되는 메서드
- 리소스(`ResultSet`, `PreparedStatement`, `Statement`, `Connection`)를 닫아준다
  - 순서에 맞게 닫아야한다 LIFO 식으로 닫으면된다
- 이를 통해 테스트 후 데이터베이스와의 연결을 종료하고 리소스를 해제하여 메모리 누수를 방지한다

### 3. **`jdbc_insert_test` 메서드**

- 두 명의 회원(admin, user)을 생성하여 데이터베이스에 `INSERT` 쿼리를 실행한다
- `genInsertMemberQuery()` 메서드를 사용하여 SQL 쿼리를 생성한다
- `stmt.executeUpdate()`를 사용하여 SQL 쿼리를 실행하고, 영향을 받은 행 수를 `result1`, `result2`에 저장한다

### 4. **`genInsertMemberQuery` 메서드**

- `Member` 객체의 `username`과 `password`를 이용해 `INSERT INTO member (username, password) VALUES ...` 형태의 SQL 쿼리를 반환한다
