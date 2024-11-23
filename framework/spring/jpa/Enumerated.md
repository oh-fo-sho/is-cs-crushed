## Enumerated

### Enum 이란?

Java의 `Enum` 은 열거형 데이터 타입으로 고정된 상수 집합을 정의할 때 사용된다. `Enum` 은 클래스처럼 동작하고 단순히 상수 값만 포함하는게 아니라 필드, 메서드, 생성자 또한 포함할 수 있다

### **특징**

1. **고정된 상수 값 집합**
   - 특정 값만 가질 수 있도록 제한
   - 상수 값들을 열거형으로 정의하여 코드의 **가독성**과 **안정성**을 높임
2. **객체처럼 동작**
   - 각 열거형은 **싱글턴 객체**로 동작
   - 추가 속성이나 메서드를 정의하여 복잡한 기능 구현 가능
3. **타입 안정성**
   - 컴파일타임에 잘못된 값을 사용할 경우 오류 발생
   - 문자열이나 정수 상수를 사용할 때의 오류를 방지

### **기본 Enum 예시**

다음은 기본적인 열거형 정의이다

```java
public enum Level {
    GOLD,
    SILVER,
    GENERAL
}
```

- 이 Enum은 회원의 레벨(Level)을 나타낸다
- `GOLD`, `SILVER`, `GENERAL`의 세 가지 값만 가질 수 있다

### Enum 속성과 메서드 추가하기

Java의 `Enum` 은 단순 상수 이상의 기능을 제공할 수 있도록 필드와 메서드를 추가할 수 있다

```java
public enum Level {
    GOLD(3, "Top tier customer with highest benefits"),
    SILVER(2, "Mid-tier customer with moderate benefits"),
    GENERAL(1, "Standard customer with basic benefits");

    private final int rank;          // 추가 속성: 레벨 순위
    private final String description; // 추가 속성: 레벨 설명

    // 생성자 (private)
    Level(int rank, String description) {
        this.rank = rank;
        this.description = description;
    }

    // Getter 메서드
    public int getRank() {
        return rank;
    }

    public String getDescription() {
        return description;
    }

    // 레벨 비교 메서드
    public boolean isHigherThan(Level other) {
        return this.rank > other.rank;
    }

    // 정적 메서드: Rank로 Level 찾기
    public static Level findByRank(int rank) {
        for (Level level : Level.values()) {
            if (level.rank == rank) {
                return level;
            }
        }
        throw new IllegalArgumentException("Invalid rank: " + rank);
    }
}
```

---

### `@Enumerated`

`@Enumerated` 어노테이션은 JPA에서 `enum` 타입 필드를 데이터베이스에 저장할 방식을 정의한다. `EnumType` 에는 `ORDINAL` 과 `STRING` 이 있다

### **1. `EnumType.ORDINAL`**

- **설명**
  `enum`의 순서 값(0부터 시작)을 데이터베이스에 저장
  - `GOLD` → 0
  - `SILVER` → 1
  - `GENERAL` → 2
- **장점**
  - 저장 공간을 적게 차지 (정수 값으로 저장되므로)
  - 기존 데이터베이스에 정수형 필드를 사용할 경우 호환성이 좋음
- **단점**
  - **열거형 상수의 순서가 변경되면 문제가 발생**
    - 예: `enum`에 새로운 값이 추가되거나 순서를 바꾸면, 기존 데이터가 잘못 매핑될 수 있음
  - 가독성이 떨어진다
  - 데이터베이스에서 숫자로 저장되기 때문에 의미를 알기 어렵다

### **2. `EnumType.STRING`**

- **설명**
  `enum` 상수의 이름(문자열)을 데이터베이스에 저장합니다
  - `GOLD` → "GOLD"
  - `SILVER` → "SILVER"
  - `GENERAL` → "GENERAL"
- **장점**
  - 열거형 상수의 **순서 변경이나 추가**가 데이터의 무결성에 영향을 미치지 않는다
  - 데이터베이스에 저장된 값이 **직관적**이고 가독성이 좋다
- **단점**
  - 저장 공간을 더 많이 차지합니다 (문자열 길이에 따라 다름)
  - 값이 저장될 때 대소문자, 스펠링 오류에 주의해야 함
