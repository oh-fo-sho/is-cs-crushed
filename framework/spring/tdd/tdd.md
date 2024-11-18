# 테스트

## 테스트 올바르게 쓰기

### FIRST 원칙

전에도 작성한 적이 있어 빠르게 훑고 넘어가자

- **FAST** - 빠르게 실행되어야 한다
- **Independent** - 독립적으로 실행되어야 한다
- **Repeatable** - 환경에 관계없이 항상 같은 결과를 보장해야한다
- **Self-validating** - 테스트는 스스로 결과를 검증하고 판단 가능해야 한다
- **Timely** - 실제 코드가 작성되기 전 혹은 작성 직후에 테스트를 작성해야 한다

### `given-when-then` 패턴을 사용한 간단한 테스트 코드

```java

class CalculatorTest {

    @Test
    void addTwoNumbers() {
        // Given
        Calculator calculator = new Calculator();

        // When
        int result = calculator.add(2, 3);

        // Then
        assertEquals(5, result, "2 + 3 should equal 5");
    }
}
```

→ 해당 테스트는 빠르고, 독립적이고, 환경에 의존하지 않고, 스스로 검증하였다.

### AssertJ

`AssertJ` 는 Java의 단위 테스트에서 사용하는 검증 라이브러리이다. 주로 JUnit과 함께 사용되어 표현력 높은 테스트 케이스를 만드는 데에 도움을 준다. `AssertJ` 는 플루언트 인터페이스를 사용하여 메서드 체이닝 방식으로 검증 문장을 작성할 수 있다.

- **플루언트 인터페이스(Fluent Interface)**
  플루언트 인터페이스는 메서드 체이닝을 통해 연속적으로 메서드를 호출할 수 있도록 설계된 인터페이스이다. 해당 스타일의 인터페이스를 사용하면 각 메서드가 **_자신을 호출한 객체를 반환하여_** 여러 메서드를 일련의 호출로 연결할 수 있게 한다.
  플루언트 인터페이스 특징
  1. 메서드 체이닝
     1. 메서드가 객체 자신을 반환하므로 여러 메서드를 한 줄에 연결해 호출 할 수 있다
  2. 가독성 증가
     1. 문장처럼 읽히는 코드 스타일의 가독성
  3. API의 직관성
     1. 연속적인 호출을 통해 의도와 맥락을 정확하게 표현

→ **`빌더 패턴`** **이 플루언트 인터페이스를 사용하는 대표적인 예**

빌더 패턴에서는 불변 객체를 생성하고, 옵션이 많은 객체를 생성(setter를 여러번 호출해야할 경우, 혹은 선택적인 필드를 나중에 지정한다던가) 할 때 주로 사용이 된다!

### AssertJ의 주요 특징

- **가독성 높은 테스트 코드**
  - `assertThat()` 메서드를 사용하여 인간이 읽기 쉬운 테스트 코드를 작성할 수 있다
- **플루언트 API**
  - 메서드 체이닝을 통해 읽기 쉽고 이해하기 쉬운 검증 문장을 만든다
- **다양한 검증 메서드**
  - 기본적인 값 검증 외에도 컬렉션, 맵, 예외, 날짜 등 다양한 데이터 유형에 대한 검증 메서드를 제공
- **향상된 오류 메시지**
  - 테스트가 실패했을 때 좀 더 직관적인 오류 메시지를 표시하여 디버깅에 도움이 된다

### AssertJ 기본 사용법

AssertJ에서는 `Assertions.assertThat()` 메서드를 사용하여 검증할 대상을 시작으로 다양한 검증 메서드를 이어서 사용할 수 있다

1. 기본 값 검증 예제

```java
import static org.assertj.core.api.Assertions.assertThat;

public class ExampleTest {

    @Test
    void testBasicAssertions() {
        int result = 10;
        assertThat(result)
            .isEqualTo(10)          // 값이 10과 같은지
            .isGreaterThan(5)       // 5보다 큰지
            .isLessThanOrEqualTo(10); // 10보다 작거나 같은지
    }
}
```

1. 컬렉션 검증 예제

```java
import static org.assertj.core.api.Assertions.assertThat;

public class CollectionTest {

    @Test
    void testCollectionAssertions() {
        List<String> fruits = List.of("apple", "banana", "orange");

        assertThat(fruits)
            .hasSize(3)                     // 크기가 3인지
            .contains("banana")             // "banana"를 포함하는지
            .containsExactly("apple", "banana", "orange") // 정확히 이 순서대로 포함하는지
            .doesNotContain("grape");       // "grape"를 포함하지 않는지
    }
}
```

1. 예외 검증 예제

```java
import static org.assertj.core.api.Assertions.assertThatThrownBy;

public class ExceptionTest {

    @Test
    void testExceptionAssertions() {
        assertThatThrownBy(() -> {
            throw new IllegalArgumentException("Invalid argument");
        })
        .isInstanceOf(IllegalArgumentException.class)    // 예외 유형 검증
        .hasMessageContaining("Invalid");                // 메시지에 "Invalid"가 포함되어 있는지
    }
}
```

## BDD(Behavior-Driven Development)

BDD는 테스트 주도 개발의 발전된 형태로 개발자와 개발자와 비기술적인 이해 관계자가 함께 소프트웨어의 행동을 정의하고 이해하는 데 중점을 둔 개발 접근 방식이다

### BDD의 특징과 장점

1. 가독성 향상
   1. BDD는 테스트 케이스를 인간 친화적인 언어로 기술하여 테스트의 의도와 소프트웨어의 행동을 직관적으로 이해할 수 있게 한다
2. 협업 강화
   1. BDD에서는 비기술적 관계자와의 협업을 통해 시스템의 요구 사항을 자연어로 표현
   2. 요구사항에 대한 오해를 줄이고 협업을 촉진
3. 요구 사항 기반 테스트
   1. BDD는 시스템이 특정 요구 사항을 어떻게 충족하는지 설명
   2. 요구 사항 변경 발생시, 테스트 쉽게 업데이트 가능
4. 핵심 행동에 집중
   1. BDD는 소프트웨어의 핵심 기능과 사용자가 기대하는 행동에 초점을 맞춤
   2. 시스템의 결과물과 행동을 기준으로 테스트 정의

### BDD 작성 방법

BDD는 일반적으로 Given-When-Then 구조를 기반으로 한 시나리오처럼 표현된다

- Given : 시스템의 초기 상태
- When : 사용자가 어떤 행동을 수행했을 때를 의미
- Then : 예상 결과를 설명

해당 구조는 자연어에 가깝기 때문에 비기술적 이해관계자들도 쉽게 이해 가능하고 각각의 시나리오가 요구 사항에 어떻게 부합하는지 명확하게 확인 가능하다

→ BDD를 위한 프레임워크도 존재하는데 Cucumber와 Gherkin 등이 그 예시이다(자연어에 가깝게 생겼음)

### JUnit을 이용한 BDD 스타일의 테스트

BDD 프레임워크 없이도 JUnit 과 같은 일반 테스트 프레임워크를 사용하여 BDD 처럼 테스트를 작성할 수 있다.

`@Nested` 는 Junit 5에서 제공하는 어노테이션이고 테스트 클래스 내에 중첩된 테스트 클래스를 사용할 수 있게 한다. 이를 사용해 테스트 구조를 논리적으로 분류하고 테스트 코드의 가독성과 유지보수성을 높일 수 있다.

### `@Nested` 어노테이션 역할

1. 테스트 그룹화
   1. 테스트를 논리적으로 관련있는 그룹으로 묶어 관리 가능
      1. 특정 메서드나 상태에 대해 여러 상황을 테스트할 경우
2. 설정 상태 공유
   1. `@Nested` 클래스를 사용하여 외부 클래스의 상태나 구성 메서드에 접근 가능
   2. 공통적인 설정은 외부 클래스에 정의하고 중첩 클래스들이 이를 활용해 테스트를 수행하도록 할 수 있음
3. 가독성과 구조 개선
   1. `@Nested` 를 사용하여 테스트 클래스 안에서 계층 구조 만듦
      1. 정확히 어떤 상황에 대한 테스트인지 명확하게 표현 가능
   2. BDD 스타일로 작성할 때 Given-When-Then 흐름을 구체적으로 나눌 수 있다

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class BankAccountTest {

    @Nested
    @DisplayName("deposit() 메서드는")
    class DepositTests {

        @Test
        @DisplayName("양수의 금액을 입금하면 잔액이 증가해야 한다")
        void shouldIncreaseBalanceWhenDepositPositiveAmount() {
            BankAccount account = new BankAccount(100);
            account.deposit(50);
            assertEquals(150, account.getBalance());
        }

        @Test
        @DisplayName("음수의 금액을 입금하면 예외가 발생해야 한다")
        void shouldThrowExceptionWhenDepositNegativeAmount() {
            BankAccount account = new BankAccount(100);
            assertThrows(IllegalArgumentException.class, () -> account.deposit(-50));
        }
    }

    @Nested
    @DisplayName("withdraw() 메서드는")
    class WithdrawTests {

        @Test
        @DisplayName("잔액보다 적은 금액을 출금하면 잔액이 감소해야 한다")
        void shouldDecreaseBalanceWhenWithdrawValidAmount() {
            BankAccount account = new BankAccount(100);
            account.withdraw(50);
            assertEquals(50, account.getBalance());
        }

        @Test
        @DisplayName("잔액보다 큰 금액을 출금하려고 하면 예외가 발생해야 한다")
        void shouldThrowExceptionWhenWithdrawExceedsBalance() {
            BankAccount account = new BankAccount(100);
            assertThrows(IllegalArgumentException.class, () -> account.withdraw(150));
        }
    }
}

```

### 테스트 코드에서 주석달기?

`Given-When-Then` 구조 자체는 BDD에서 유래 했다고 한다. 사실 코드에 주석을 자주 달진 않는데(코드 개발중일 땐 변경점이 추후에 생길 경우 주석 또한 관리해줘야 하는게 힘들다.. 사실은 귀찮아서..) 테스트 코드에서는 테스트의 의도를 명확히 하고 구조적으로 일관성을 지키는데에 도움이 되기 때문에 달아주는게 좋을 것 같기도 하다. 하지만 BDD 자체가 비기술직 인원들도 보고 파악을 하는데에 중점이 있기 때문에 그 의도를 고려하면 코드로 작성하는 테스트에는 어느정도 필요할 것 같다. `@DisplayName` 을 달아주는 것도 테스트 의도 표현에 유용할 것 같다.

<aside>
💡

**결론** : 테스트 코드에 주석도 달고 `@DisplayName` 으로 어떤 테스트인지 명확히 하자

</aside>
