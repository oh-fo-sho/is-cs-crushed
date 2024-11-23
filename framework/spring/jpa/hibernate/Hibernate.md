# Hibernate

### 정의 및 역할

- Hibernate 는 JPA를 구현한 대표적인 ORM 프레임워크이다
- 스프링 프레임워크에서 기본 ORM으로 채택되어 많이 사용된다

### 특징

1. JPA 구현체
   1. JPA의 표준 명세를 따르고 고유한 확장 기능 또한 제공한다
2. Lazy/Eager Loading
   1. 엔티티의 연관 데이터를 지연 혹은 즉시 로딩할 수 있는 전략을 지원한다
3. 캐싱
   1. 1차 캐시(영속성 컨텍스트)와 2차 캐시(외부 캐시 연동)를 활용해 데이터베이스의 부하를 줄인다
4. SQL 자동 생성 및 최적화:
   1. Hibernate는 기본 SQL 뿐만 아니라 JPQL(Java Persistence Query Language)을 지원하며 JPQL을 통해 객체 지향적인 쿼리를 작성할 수 있다
5. 다양한 데이터베이스 지원
   1. 다중 데이터베이스 환경에서 SQL 방언(Dialect)을 처리하여 동일한 코드를 기반으로 여러 데이터베이스에서 실행 가능하도록 한다

### 단점

- 복잡한 쿼리 처리의 한계
  - 복잡한 통계 쿼리나 다중 테이블 조인 작업시 수동 SQL 작성이 필요하다
- 초반 학습 곡선
  - Hibernate의 동작방식(영속성 컨텍스트, 캐시, 연관관계 설정 등)을 이해하는 데에 시간이 소요된다

## Hibernate, JPA 없이 사용하기

### **`persistence.xml`의 역할**

- **`persistence.xml` 파일**은 JPA 표준에서 사용되는 설정 파일로, 애플리케이션의 영속성 관련 설정 정보 보유
- JPA 구현체인 하이버네이트를 직접 사용하려면 이 파일을 작성해야 하며, 기본적으로 `/resources/META-INF/persistence.xml` 경로에 위치해야 한다
- 이 파일은 영속성 유닛(Persistence Unit)을 정의하며, JPA 및 하이버네이트가 데이터베이스와 상호작용할 수 있도록 설정 정보를 제공한다

### **`persistence.xml` 구조와 주요 설정**

### **`<persistence>` 태그**

- 최상위 태그로, 여러 개의 **영속성 유닛**(`persistence-unit`)을 정의할 수 있다
- 프로젝트에서 데이터베이스 연결이 필요한 경우, 주로 하나의 영속성 유닛을 정의하지만, 다중 데이터베이스를 사용해야 한다면 여러 개의 유닛을 정의할 수도 있다

### **`<persistence-unit>` 태그**

- **영속성 유닛**은 데이터베이스 연결 정보 및 영속성 관련 설정을 묶어놓은 단위
- 데이터베이스별로 별도의 영속성 유닛을 정의하여 사용할 수 있다
- 주요 속성
  - `name`
    - 영속성 유닛의 이름. 애플리케이션에서 유닛을 참조할 때 사용
  - `transaction-type`
    - 트랜잭션 관리를 정의. `RESOURCE_LOCAL`(기본) 또는 `JTA`(Java Transaction API)

### **`<properties>` 태그**

- 하이버네이트의 동작을 제어하는 설정 정보를 정의
- 주요 속성
  - `javax.persistence.jdbc.url`: 데이터베이스 연결 URL
  - `javax.persistence.jdbc.user`: 데이터베이스 사용자 이름
  - `javax.persistence.jdbc.password`: 데이터베이스 비밀번호
  - `javax.persistence.jdbc.driver`: JDBC 드라이버 클래스 이름
  - `hibernate.dialect`: 하이버네이트 방언. 데이터베이스 벤더에 따라 적절한 방언을 설정

### **`<class>` 태그**

- 영속성 유닛에서 관리할 엔티티 클래스를 지정
- 반드시 엔티티 클래스의 FQCN(Fully Qualified Class Name)을 입력해야 하며, 모든 엔티티를 나열해야 한다

## `persistence.xml`

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<persistence xmlns="https://jakarta.ee/xml/ns/persistence"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             version="3.0"
             xsi:schemaLocation="https://jakarta.ee/xml/ns/persistence
             https://jakarta.ee/xml/ns/persistence/persistence_3_0.xsd">
    <persistence-unit name="cooking-simulation">
        <properties>
            <property name="jakarta.persistence.jdbc.driver" value="com.mysql.cj.jdbc.Driver" />
            <property name="jakarta.persistence.jdbc.user" value="cassidy" />
            <property name="jakarta.persistence.jdbc.password" value="65"/>
            <property name="jakarta.persistence.jdbc.url" value="jdbc:mysql://localhost:3307/est_jdbc" />

            <!-- configure logging -->
            <property name="eclipselink.logging.level" value="INFO" />
            <property name="eclipselink.logging.level.sql" value="FINE" />
            <property name="eclipselink.logging.parameters" value="true" />
        </properties>
    </persistence-unit>
</persistence>
```

`persistence.xml` 파일은 JPA(Java Persistence API)에서 데이터베이스 연결과 관련된 설정을 정의하는 파일이다. 꼭 하이버네이트가 아니더라도 JPA 구현체를 사용할 때 사용된다

- XML 선언: 이 구문은 XML 파일의 버전(1.0)과 인코딩 방식(UTF-8)을 정의한다
- `<persistence>` : JPA 설정을 정의하는 루트 요소
- `xmlns`와 `xsi:schemaLocation`: XML 네임스페이스와 XML 스키마 위치를 정의한다. 이는 JPA의 XML 설정 파일을 올바르게 해석하기 위해 사용된다고 한다
- `version="3.0"`: JPA의 버전 정보를 의미한다
- `<persistence-unit>`: 데이터베이스 연결과 관련된 설정을 묶는 단위
  - 여러 설정을 정의하는 것도 가능하다
- `<properties>`: JPA 설정에서 사용할 여러 가지 속성(Property)을 정의하는 영역이다
- `"com.mysql.cj.jdbc.Driver"`: MySQL JDBC 드라이버 클래스
  - **External Libraries 에서 Driver 경로를 적어주면 된다**
- `"cassidy"`: 데이터베이스에 접속할 때 사용할 사용자 이름
- `"65"`: 지정된 사용자(`cassidy`)의 비밀번호
- `name="eclipselink.logging.level` : JPA 구현체인 EclipseLink의 로깅 레벨이다
- `name="eclipselink.logging.level.sql"` : SQL 쿼리 로그의 로깅 레벨이다
- `name="eclipselink.logging.parameters"` : SQL 쿼리에 사용되는 파라미터를 로깅할 지에 대한 설정이다
