## Sequence

```java
package domain.ex2._3;

import jakarta.persistence.*;

@Entity
@SequenceGenerator(
        name = "custom_sequence_generator",
        sequenceName = "account_seq",
        initialValue = 1, allocationSize = 1
)
public class Account {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "custom_sequence_generator")
    private Long id;

    private String name;

}
```

### JPA의 Squence 전략

`@SequenceGenerator` 와 `@GeneratedValue(strategy = GenerationType.SEQUENCE)` 는 JPA에서 시퀀스를 활용하여 기본 키를 자동으로 생성할 때 사용하는 어노테이션이다. 이를 사용하면 JPA가 데이터베이스 시퀀스를 통해 ID 값을 관리하게 된다

- **시퀀스(Sequence)란?**
  - 시퀀스는 데이터베이스에서 제공하는 객체
    - 순차적으로 증가하는 고유 번호 생성
  - 시퀀스는 특정 테이블에 종속되지 않고 여러 테이블에서 공유 가능
  - Oracle, PostgreSQL 같은 DBMS는 시퀀스를 기본적으로 지원

### 관련 어노테이션

### **1. `@SequenceGenerator`**

- JPA에서 **시퀀스를 정의**하기 위한 어노테이션
- 시퀀스와 관련된 설정을 제공하며, 생성 전략을 정의
- 주요 속성
  - **`name`**: JPA에서 사용할 시퀀스 생성기 이름
  - **`sequenceName`**: 데이터베이스에 존재하는 시퀀스의 이름. JPA가 이를 참조해 값을 생성
  - **`initialValue`**: 시퀀스가 시작하는 초기값 (디폴트: 1)
  - **`allocationSize`**: 한번에 시퀀스에서 미리 할당받을 ID의 개수. 기본값은 50으로 성능 최적화를 위해 사용

### **예제 코드에서의 설정**

```java
@SequenceGenerator(
        name = "custom_sequence_generator", // JPA에서 사용할 이름
        sequenceName = "account_seq",       // 데이터베이스의 시퀀스 이름
        initialValue = 1,                   // 초기값
        allocationSize = 1                  // 한 번에 할당받는 개수 (1로 설정하면 매번 DB에서 가져옴)
)
```

- **JPA에서 `custom_sequence_generator`라는 이름으로 시퀀스를 정의**
- 데이터베이스에 **`account_seq`라는 이름의 시퀀스**가 존재한다고 가정

### **2. `@GeneratedValue(strategy = GenerationType.SEQUENCE)`**

- JPA에서 기본 키의 **자동 생성 전략**을 설정하는 어노테이션
- `strategy = GenerationType.SEQUENCE`는 데이터베이스 시퀀스를 사용해 ID 값을 생성하도록 설정
- **`generator`** 속성은 `@SequenceGenerator`의 `name`과 연결해 사용

### **예제 코드에서의 설정**

```java
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "custom_sequence_generator")
```

- 기본 키 생성 전략을 **시퀀스 기반**으로 설정
- `generator = "custom_sequence_generator"`로 지정된 시퀀스 생성기를 참조

### **동작 과정**

1. JPA가 엔티티를 저장할 때, 시퀀스를 통해 고유 키를 생성
2. 시퀀스를 참조하여 새로운 값을 할당
   - `allocationSize = 1`로 설정된 경우, **매번 시퀀스에서 값을 가져옴**
     - 더 높게 설정할 경우 할당 사이즈 만큼 미리 받아와서 사용 가능
   - 성능 최적화를 위해 `allocationSize`를 높이면, 한 번에 여러 ID를 가져와 메모리에서 관리
3. 시퀀스 값을 기본 키로 설정하여 데이터베이스에 저장

### DDL 생성 예시

```java
CREATE SEQUENCE account_seq
START WITH 1
INCREMENT BY 1;

CREATE TABLE Account (
    id BIGINT NOT NULL,
    name VARCHAR(255),
    PRIMARY KEY (id)
);
```

위 엔티티는 이와 같은 DDL과 시퀀스 생성 SQL을 만든다

## TableSequence

```java
package domain.ex2._3;

import jakarta.persistence.*;

@Entity
@Table(name = "other_account")
@TableGenerator(
        name = "other_account_table_generator",
        table = "other_account_sequence",
        pkColumnName = "account_seq", allocationSize = 1
)
public class OtherAccount {

    @Id
    @GeneratedValue(strategy = GenerationType.TABLE, generator = "other_account_table_generator")
    private Long id;
}

```

### JPA의 **TABLE 전략**

`@TableGenerator`와 `@GeneratedValue(strategy = GenerationType.TABLE)`는 JPA에서 **테이블을 활용하여 기본 키를 자동 생성**할 때 사용하는 어노테이션이다. 이는 **시퀀스를 지원하지 않는 데이터베이스에서 시퀀스와 유사한 동작**을 구현하기 위해 설계된 전략이다

- **TABLE 전략이란?**
  - 기본 키를 **전용 테이블**에 저장하고, 이를 통해 고유한 ID를 생성하는 방식
  - **시퀀스를 흉내 내는 전략**으로, 시퀀스를 지원하지 않는 데이터베이스(MySQL 등)에서 사용
  - JPA는 테이블에 **현재 키 값**을 저장하고 이를 업데이트하여 고유 키를 생성

### **어노테이션 설명**

### **1. `@TableGenerator`**

- **키 생성 테이블**을 정의하고 설정하는 어노테이션
- 주요 속성
  - **`name`**: JPA에서 사용할 테이블 생성기 이름
  - **`table`**: 기본 키 값을 관리할 테이블 이름
  - **`pkColumnName`**: 테이블에서 키 이름(컬럼명)을 저장하는 열
  - **`valueColumnName`**: 테이블에서 현재 키 값을 저장하는 열 (기본값: `NEXT_VAL`)
  - **`allocationSize`**: 키를 한 번에 얼마나 할당받을지 결정 (기본값: 50)

### **예제 코드에서의 설정**

```java
@TableGenerator(
        name = "other_account_table_generator", // JPA에서 사용할 생성기 이름
        table = "other_account_sequence",       // 키 값을 저장할 테이블 이름
        pkColumnName = "account_seq",           // 테이블의 기본 키 컬럼
        allocationSize = 1                      // 매번 테이블에서 가져오는 키의 개수
)
```

- `other_account_table_generator`라는 이름의 키 생성기를 정의
- `other_account_sequence`라는 테이블에 키 값 저장
- 테이블의 `account_seq` 열을 통해 현재 키를 관리

### **2. `@GeneratedValue(strategy = GenerationType.TABLE)`**

- 기본 키를 **TABLE 전략**을 사용해 생성하도록 설정
- `generator` 속성으로 `@TableGenerator`의 `name`과 연결

### **예제 코드에서의 설정**

```java
@GeneratedValue(strategy = GenerationType.TABLE, generator = "other_account_table_generator")
```

- 테이블 기반으로 기본 키를 생성
- `generator`로 `other_account_table_generator` 참조

### **동작 과정**

1. JPA가 `other_account_sequence` 테이블에 접근
2. `account_seq` 열에서 현재 키 값을 읽음
3. 읽은 값을 새 키로 사용하고, 다음 키 값으로 업데이트
   - **`allocationSize` = 1**로 설정되어 있으므로, 키를 하나씩 증가시킴
   - 성능 최적화를 위해 `allocationSize`를 증가시키면, 메모리에서 키를 관리하고 필요한 경우에만 DB에 접근
4. 생성된 키 값을 엔티티의 ID로 사용

### **키 생성 테이블 구조 예시**

다음은 `@TableGenerator`에 의해 생성될 수 있는 테이블 구조

```sql
CREATE TABLE other_account_sequence (
    account_seq VARCHAR(255) NOT NULL, -- 키 이름
    next_val BIGINT,                   -- 다음 키 값
    PRIMARY KEY (account_seq)
);

-- 초기 데이터
INSERT INTO other_account_sequence (account_seq, next_val) VALUES ('other_account', 1);
```

### **DDL 생성 예시**

JPA에 의해 다음과 같은 DDL과 데이터가 생성될 수 있다

```sql
CREATE TABLE other_account (
    id BIGINT NOT NULL,
    name VARCHAR(255),
    PRIMARY KEY (id)
);
```
