## `ManyToOne` , `OneToMany`

```java
package domain.ex3._1;

import jakarta.persistence.*;
import lombok.Getter;

import java.util.List;

@Entity
@Getter
@Table(name = "teams")
public class Team {

    @Id
    @Column(name = "team_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    @OneToMany(mappedBy = "team")
    private List<Player> players;
}
```

```java
package domain.ex3._1;

import jakarta.persistence.*;
import lombok.Getter;

@Entity
@Getter
@Table(name = "players")
public class Player {
    @Id
    @Column(name = "player_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @JoinColumn(name = "team_id")
    @ManyToOne(fetch = FetchType.LAZY)
    private Team team;
}

```

### 사용된 어노테이션

### **1) `@Entity`**

- **설명**
  `@Entity`는 해당 클래스가 JPA 엔티티임을 의미. 이 어노테이션이 붙은 클래스는 데이터베이스의 테이블과 매핑됩니다.
- **사용된 위치**
  `Player`와 `Team` 클래스

### **2) `@Id`**

- **설명**
  `@Id`는 해당 필드가 엔티티의 기본 키(Primary Key)임을 의미
- **사용된 위치**
  `Player` 클래스의 `id`와 `Team` 클래스의 `id`

### **3) `@GeneratedValue`**

- **설명**
  `@GeneratedValue`는 기본 키를 자동으로 생성하는 방식을 지정합니다. `strategy = GenerationType.IDENTITY`는 데이터베이스에서 기본 키를 자동으로 생성(예: MySQL의 `AUTO_INCREMENT`)
- **사용된 위치**
  `Player` 클래스의 `id`와 `Team` 클래스의 `id`

### **4) `@ManyToOne`**

- **설명**:`@ManyToOne`은 **다대일 관계 (Many-to-One)** 를 설정하는 어노테이션이다 한 엔티티가 다른 엔티티와 다대일 관계로 매핑될 때 사용한다
  - 예: 여러 `Player`가 하나의 `Team`에 속할 수 있다
  - `fetch = FetchType.LAZY` 옵션이 지정되어 있어, 이 관계를 사용할 때 `Team` 엔티티는 필요할 때 로딩된다 (즉, 지연 로딩)
- **사용된 위치**:`Player` 클래스의 `team` 필드

### **5) `@JoinColumn`**

- **설명**
  `@JoinColumn`은 외래 키를 관리하는 어노테이션이다 이 어노테이션을 통해 외래 키 컬럼의 이름을 지정할 수 있다. `name = "team_id"`로 외래 키 컬럼을 지정하여, `Player` 엔티티의 `team` 필드가 `Team` 엔티티와 연결된다는 것을 나타낸다
- **사용된 위치**
  `Player` 클래스의 `team` 필드

### **6) `@OneToMany`**

- **설명**
  `@OneToMany`는 **일대다 관계 (One-to-Many)** 를 설정하는 어노테이션이다 하나의 엔티티가 여러 엔티티와 관계를 맺는 경우 사용된다
  - 예: 하나의 `Team`은 여러 `Player`를 가질 수 있음
  - `mappedBy = "team"`은 `Player` 엔티티의 `team` 필드가 `Team` 엔티티와의 관계를 관리한다는 의미이다
- **사용된 위치**
  `Team` 클래스의 `players` 필드

### **7) `@Table`**

- **설명**
  `@Table`은 해당 엔티티가 매핑될 데이터베이스 테이블의 이름을 지정한다
- **사용된 위치**
  `Player` 클래스와 `Team` 클래스에서 테이블 이름을 각각 `players`, `teams`로 지정

### 관계 설명

### **1) `ManyToOne` (다대일 관계)**

- **설명**
  `@ManyToOne` 어노테이션은 **다대일 관계 (Many-to-One)** 를 설정
  - 한 `Player`가 여러 `Team` 중 하나에 속할 수 있다는 것을 의미한다
  - `Player`는 여러 `Player` 엔티티들이 하나의 `Team`에 속하므로, `Player` 엔티티에는 `ManyToOne` 관계가 설정됨. `Team`이 여러 `Player`를 가질 수 있는 구조
- **연관 관계**
  `Player`와 `Team`은 다대일 관계입니다. 여러 `Player`는 하나의 `Team`에 속할 수 있다

### **2) `OneToMany` (일대다 관계)**

- **설명**
  `@OneToMany` 어노테이션은 **일대다 관계 (One-to-Many)** 를 설정한다. 하나의 `Team`은 여러 `Player`를 가질 수 있다는 것을 의미한다. `Team`은 여러 `Player`와 연결된다
- **연관 관계**
  `Team`과 `Player`는 일대다 관계이다. 하나의 `Team`은 여러 `Player`를 가질 수 있다. `mappedBy = "team"`은 `Player` 엔티티에서 `team` 필드가 관계의 주체라는 것을 나타낸다

### **Lazy Loading**

- **Lazy Loading**이란 관련된 엔티티를 실제로 사용할 때까지 데이터를 로딩하지 않는 방식이다. 관련된 엔티티는 필요할 때 처음으로 접근할 때 데이터베이스에서 로드된다
  - **`fetch = FetchType.LAZY`**:`@ManyToOne(fetch = FetchType.LAZY)`에서 `fetch`를 `LAZY`로 설정하면, `Player` 객체가 로드될 때 관련된 `Team` 객체는 즉시 로드되지 않고, `Player` 객체를 참조하는 시점에만 로딩된다
- **장점**
  - 초기 로딩 성능을 향상시킬 수 있다. 모든 관련 데이터를 한 번에 로드하는 대신, 필요한 시점에만 로드되므로 불필요한 데이터 로딩을 방지할 수 있다
- **단점**
  - 이후 `team`에 접근할 때마다 데이터베이스에서 해당 데이터를 다시 쿼리하는 오버헤드가 발생할 수 있다. 이를 "N+1 쿼리 문제"라고도 한다
- **Eager Loading**
  - 반대로 `fetch = FetchType.EAGER`로 설정하면, 관련된 엔티티가 `Player` 객체를 로드할 때 즉시 로딩된다.
  - `Player`를 조회할 때 관련된 `Team`도 즉시 조회가능
- **Lazy Loading 동작 과정**
  1. **프록시 객체 생성**

     `@ManyToOne(fetch = FetchType.LAZY)` 또는 `@OneToMany(fetch = FetchType.LAZY)`와 같은 설정을 하면, 해당 연관 객체는 **프록시 객체**로 만들어진다

     이 프록시 객체는 실제 엔티티를 참조하지만, 실제 데이터는 지연 로딩 방식으로 불러오는 것

  2. **실제 데이터 조회**

     프록시 객체가 **초기화되기 전에** 연관된 객체를 참조하려고 하면, JPA는 실제 데이터를 로딩하기 위해 데이터베이스에 쿼리를 실행한다. 이때 실제 엔티티 객체가 로딩되고, 이후에는 더 이상 프록시 객체로 동작하지 않고, 실제 엔티티 객체로 동작한다

     더 구체적인 내용은 이전 TIL에 적어둔 바 있다
