## 엔티티 (Entity)

엔티티는 데이터베이스 테이블에 매핑되는 클래스이다.

- **@Entity**: 클래스가 엔티티임을 나타낸다
- **@Id**: 엔티티의 고유 식별자(PK)를 정의, **식별자는 반드시 유일해야 하며, Hibernate는 이를 통해 각 객체를 구분한다**
- **기타 매핑 어노테이션**
  - @Column, @Table 등으로 컬럼이나 테이블 정보를 매핑
  - @GeneratedValue 등을 사용해 식별자 자동 생성 전략 설정 가능

# JPA - Entity 사용하기

## `@Entity`

- **목적** : 해당 클래스를 JPA 엔티티로 지정하여 데이터베이스와 매핑되도록 한다
- **기본 요구 사항**
  - **기본 생성자** : 파라미터가 없는 기본 생성자가 반드시 있어야 한다
  - **접근 제어자** : 기본 생성자의 접근 제어자는 `public` 또는 `protected`이어야 한다
  - **final 클래스 불가능** : `final`, `enum`, `interface`, `inner` 클래스는 엔티티로 사용 불가능
  - **final 필드 불가능** : 엔티티의 필드는 `final`을 사용할 수 없다. 왜냐하면 JPA는 엔티티 객체를 수정할 수 있어야 하는데, `final` 필드는 불변 객체로 취급되기 때문이다

## `@Table`

- **목적**: 엔티티 클래스가 매핑될 테이블의 이름을 지정할 수 있다
- **기본 테이블명**
  - 테이블명을 명시하지 않으면 클래스 이름이 테이블명으로 사용된다
- **옵션**
  - `name` : 테이블 이름 지정
  - `schema` : 테이블이 위치한 스키마 지정
  - `catalog` : 테이블이 위치한 카탈로그 지정
  - `uniqueConstraints` : 유니크 제약조건을 추가할 수 있다

## `@Id` & `@GeneratedValue`

- **목적** 엔티티의 기본키(primary key)를 정의하고, 기본키 값을 자동으로 생성할 방법을 지정한다
- **`@GeneratedValue` 옵션**
  1. **IDENTITY**: 기본키가 자동 증가하는 방식으로 설정 (MySQL, MariaDB 등에서 사용)
  2. **SEQUENCE**: 시퀀스를 사용하는 방식 (Oracle, PostgreSQL 등에서 사용)
  3. **TABLE**: 테이블을 이용한 방식 (시퀀스가 없는 DB에서 사용)
  4. **AUTO** : JPA 구현체가 자동으로 전략을 선택
     1. AUTO의 경우 시퀀스 테이블이름 등 세부 사항을 개발자가 확인하기 어려워진다

## `@Column`

- **목적** : 엔티티 클래스의 필드를 데이터베이스 컬럼에 매핑
- **옵션**
  - `name`: 컬럼명 지정
  - `nullable`: 해당 컬럼이 `NULL`을 허용할지 여부
  - `unique`: 유니크 제약조건을 설정
  - `length`: 문자열 컬럼의 최대 길이
  - `precision` / `scale`: 숫자형 컬럼의 정밀도와 스케일
  - `insertable`, `updatable`: 해당 필드가 INSERT/UPDATE 시 포함될지 여부

## `@Enumerated`

- **목적**: Java `enum` 타입을 데이터베이스에 저장할 때 사용한다
- **옵션**
  - `EnumType.ORDINAL`: `enum`의 순서값을 저장
  - `EnumType.STRING`: `enum`의 이름을 문자열로 저장
- **주의**
  - `ORDINAL` 방식은 `enum`에 값이 추가될 때 순서가 변경될 수 있으므로 주의해야 한다
  - `STRING` 방식은 안전하지만 용량 상의 이점은 적다

## `@Transient`

- **목적**
  - 해당 필드를 데이터베이스와 매핑하지 않도록 지정한다
  - 데이터베이스에 저장하거나 조회하지 않으며, 객체 내에서만 사용된다
- **용도:** 계산된 값, 임시 값 등을 필드에 저장할 때 사용된다

```java
package domain.ex2._1;

import jakarta.persistence.*;
import lombok.AccessLevel;
import lombok.Builder;
import lombok.NoArgsConstructor;

@Entity
@Table(name = "article")
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class Post {
    @Id
    @Column(unique = true)
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "title", nullable = false, length = 100)
    private String title; // string은 varchar있으면 varchar, 없으면 varchar2

    @Column(name = "contents", columnDefinition = "TEXT")
    private String contents;

    @Builder
    public Post(String title, String contents) {
        this.title = title;
        this.contents = contents;
    }
}
```

### **1. 클래스 어노테이션**

- **`@Entity`**
  - 이 클래스를 JPA에서 **엔티티**로 사용하겠다는 선언
  - 해당 클래스는 데이터베이스 테이블과 매핑됨
  - 이 어노테이션이 없으면 JPA는 이 클래스를 무시
  - **`@Id` 를 필요로 함**
  - **기본생성자도 반드시 필요로 함**
    - **JPA에서 기본 생성자가 필요한 이유**
      ### **JPA에서 기본 생성자가 필요한 이유**
      1. **리플렉션을 통한 객체 생성**
         - JPA는 내부적으로 리플렉션(Reflection)을 사용하여 객체를 생성한다
         - 이 과정에서 개발자가 직접 생성자를 호출하지 않고, JPA가 **기본 생성자**를 통해 객체를 인스턴스화시킨다
      2. **프록시 객체의 생성**
         - JPA는 지연 로딩(Lazy Loading)을 위해 엔티티 대신 프록시 객체를 생성할 때 기본 생성자를 사용한다
         - 프록시 객체는 엔티티를 상속받아 동작하기 때문에, **부모 클래스의 기본 생성자가 반드시 필요하다**
      ***
      ### **기본 생성자의 접근 제한자**
      1. **`protected` 또는 `public`이어야 하는 이유**
         - 프록시 객체는 해당 엔티티를 상속받아 생성되므로, 기본 생성자가 상속된 클래스에서도 호출 가능해야 한다
         - JPA 스펙에 따르면 기본 생성자의 접근 제한자는 반드시 **`public`** 또는 **`protected`**여야 한다
           - **`protected`**: 외부에서 직접 호출은 제한하면서, 상속받은 프록시 클래스에서는 호출 가능
           - **`public`**: 기본 생성자가 외부에서 호출 가능
         - `private`로 설정하면 JPA가 이를 호출할 수 없어 런타임 오류가 발생
      2. **`@NoArgsConstructor(access = AccessLevel.PROTECTED)`**
         - 위 코드처럼 Lombok을 활용하면, 기본 생성자를 쉽게 생성하면서 접근 제한자를 `protected`로 설정 가능
         - 이는 JPA의 요구 사항을 만족하면서도, 외부에서의 불필요한 호출을 방지할 수 있는 좋은 관례이다 → 실무에서 많이 사용
      ***
      ### **프록시 객체와 기본 생성자의 관계**
      - JPA가 프록시 객체를 생성할 때
        1. **프록시 클래스가 엔티티를 상속받아 생성됨**
        2. 프록시 객체 내부에는 실제 엔티티를 로딩할 때 사용할 메커니즘이 포함됨
        3. 이 과정에서 프록시 클래스가 기본 생성자를 호출해야 하기 때문에 **`protected` 또는 `public` 접근 수준**이 요구됨
- **`@Table(name = "article")`**
  - 엔티티와 매핑될 **테이블 이름**을 지정
  - `name = "article"`은 데이터베이스의 테이블 이름을 `article`로 설정
- **`@NoArgsConstructor(access = AccessLevel.PROTECTED)`**
  - **기본 생성자**를 생성하되, 접근 수준을 `protected`로 제한
  - JPA는 기본 생성자가 반드시 필요하며, **외부에서 직접 생성자를 호출하지 못하도록 제한**하기 위해 사용

---

### **2. 필드 어노테이션**

- **`@Id`**
  - 이 필드를 엔티티의 기본 키(Primary Key)로 지정
  - `id` 필드는 테이블의 PK로 매핑됨
- **`@GeneratedValue(strategy = GenerationType.IDENTITY)`**
  - 기본 키 값을 **자동 생성**하도록 설정
  - `GenerationType.IDENTITY`는 데이터베이스의 **AUTO_INCREMENT**를 사용하여 값을 생성
- **`@Column`**
  - 테이블의 컬럼과 엔티티 필드를 매핑
  - DDL 생성 시에만 영향을 미치며, **데이터 유효성 검증은 애플리케이션 코드에서 별도로 처리**해야 함
    - **속성 설명**
      - `name`: 데이터베이스의 컬럼 이름 설정
      - `nullable`: `false`일 경우, DDL에서 `NOT NULL` 제약 조건을 추가
      - `length`: 문자열의 최대 길이를 지정 (`VARCHAR` 길이 제한)
      - `columnDefinition`: 데이터 타입을 명시적으로 지정 (`TEXT`, `BLOB` 등)
    **`title` 필드**
    - `@Column(name = "title", nullable = false, length = 100)`
      - **테이블의 title 컬럼과 매핑**되며, `NOT NULL`과 최대 길이 100 설정
      - DDL에서 생성
        ```sql
        title VARCHAR(100) NOT NULL
        ```
    **`contents` 필드**
    - `@Column(name = "contents", columnDefinition = "TEXT")`
      - 테이블의 contents 컬럼과 매핑
      - `TEXT` 타입으로 명시적 정의
      - DDL에서 생성
        ```sql
        contents TEXT
        ```

---

### **3. 생성자**

- **`@Builder`**
  - Lombok 어노테이션으로 **빌더 패턴**을 사용하여 객체 생성
  - 생성자 대신 `Post.builder().title("...").contents("...").build()` 형태로 객체 생성 가능
  - 빌더 패턴은 필드가 많은 객체 생성 시 가독성과 유연성을 높임
