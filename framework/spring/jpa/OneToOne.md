## `OneToOne`

```java
package domain.ex3._2;

import jakarta.persistence.*;
import lombok.Getter;

@Getter
@Entity
public class Locker {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToOne(mappedBy = "Locker")
    private Owner owner;
}
```

```java
package domain.ex3._2;

import jakarta.persistence.*;
import lombok.Getter;

@Entity
@Getter
@Table(name = "owners")
public class Owner {
    @Id
    @Column(name = "owners_id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @OneToOne
    @JoinColumn(name = "locker_id")
    private Locker locker;
    private String name;
}
```

### 어노테이션 설명

### **1) `@Entity`**

- **설명**
  `@Entity` 어노테이션은 해당 클래스가 JPA 엔티티임을 나타냅니다. 이 어노테이션이 붙은 클래스는 데이터베이스 테이블과 매핑
- **사용된 위치**
  `Locker`, `Owner` 클래스

### **2) `@Id`**

- **설명**
  `@Id`는 해당 필드가 엔티티의 기본 키(Primary Key)임을 지정
- **사용된 위치**
  `Locker` 클래스의 `id`, `Owner` 클래스의 `id`

### **3) `@GeneratedValue`**

- **설명**
  `@GeneratedValue`는 기본 키의 값을 자동으로 생성할 때 사용된다. `strategy = GenerationType.IDENTITY`는 데이터베이스에서 자동으로 증가하는 값(예: MySQL의 `AUTO_INCREMENT`)을 사용하여 기본 키를 생성한다
- **사용된 위치**
  `Locker` 클래스의 `id`, `Owner` 클래스의 `id`

### **4) `@OneToOne`**

- **설명**
  `@OneToOne`은 **일대일 관계**를 설정하는 어노테이션이다. 한 객체가 다른 객체와 1:1로 매핑된다는 것을 나타낸다
- **사용된 위치**
  - `Owner` 클래스에서 `locker` 필드
  - `Locker` 클래스에서 `owner` 필드

### **5) `@JoinColumn`**

- **설명**
  `@JoinColumn`은 외래 키(Foreign Key) 컬럼을 정의하는 데 사용된다. `name` 속성은 외래 키 컬럼의 이름을 지정한다
- **사용된 위치**
  `Owner` 클래스의 `locker` 필드에서 `@JoinColumn(name = "locker_id")`로 외래 키 컬럼을 정의한다. 이 컬럼은 `Locker` 테이블의 기본 키와 연결된다

### **6) `@MappedBy`**

- **설명**
  `@MappedBy`는 양방향 연관 관계에서 반대쪽 엔티티에서 이미 외래 키를 관리하고 있음을 나타낸다 즉, `Locker`에서 `Owner`로의 관계를 `Owner` 객체의 `locker` 필드에서 관리하고 있다는 것을 나타낸다
- **사용된 위치**
  `Locker` 클래스의 `owner` 필드에 `@OneToOne(mappedBy = "locker")`가 사용된다 이는 `Owner` 클래스에서 외래 키를 관리하고 있음을 의미한다

### **7) `@Table`**

- **설명**
  `@Table`은 엔티티 클래스가 매핑될 테이블 이름을 정의합니다. 이 어노테이션을 사용하여 테이블 이름을 변경할 수 있다
- **사용된 위치**
  `Owner` 클래스에 `@Table(name = "owners")`가 사용되어 테이블 이름을 `owners`로 지정한다

### 관계 설명

### **1) `@OneToOne` 관계**

- **일대일 관계 (One-to-One)**
  - **일대일 관계**란 하나의 엔티티가 다른 하나의 엔티티와 매핑된 관계다
  - 예: 한 `Owner`는 하나의 `Locker`를 가질 수 있고, 하나의 `Locker`는 하나의 `Owner`를 가질 수 있다
  - `Owner`와 `Locker` 엔티티는 **양방향** 관계를 가진다
    - `Owner` 엔티티에서 `Locker`와의 관계를 `locker` 필드로 정의하고, `Locker` 엔티티에서 `Owner`와의 관계를 `owner` 필드로 정의한다

### **2) 양방향 관계에서의 `mappedBy`와 `JoinColumn`**

- **`mappedBy`**
  - `@MappedBy`는 관계의 주체를 설정하는 어노테이션이다 여기서는 `Locker` 엔티티가 관계의 주체가 아니므로 `Owner` 클래스의 `locker` 필드가 주체임을 의미한다
  - `Locker`에서 `@OneToOne(mappedBy = "locker")`는 `Owner` 클래스에서 외래 키를 관리한다고 명시한다
- **`JoinColumn`**
  - `@JoinColumn`은 외래 키를 관리하는 어노테이션으로, `Owner` 엔티티의 `locker` 필드에서 외래 키 `locker_id`가 `Locker` 엔티티의 기본 키와 연결되도록 설정한다
