### 전략 패턴

전략 패턴은 특정 알고리즘을 캡슐화하고, 해당 알고리즘을 사용하는 클라이언트 코드에서 독립적으로 변경할 수 있도록 하는 행동 디자인 패턴이다. 이 패턴은 다양한 알고리즘을 서로 교환할 수 있게 해 주며, 클라이언트는 알고리즘을 변경하는 것에 대해 신경 쓸 필요 없이 동일한 방식으로 알고리즘을 사용할 수 있다.

> **전략 패턴 구성 요소**
>
> - **Context** 클라이언트가 사용하는 인터페이스로, 전략 객체를 설정하고 호출하는 역할
> - **Strategy** 전략을 정의하는 인터페이스, 다양한 알고리즘을 구현할 수 있는 메서드를 포함함
> - **ConcreteStrategy** `Strategy` 인터페이스를 구현하는 구체적인 알고리즘 클래스, 여러 개의 구체적인 전략이 존재할 수 있다

```java
// 전략 인터페이스
interface DiscountStrategy {
    double applyDiscount(double price); // 할인 적용 메서드
}

// 구체적인 전략 클래스: 정률 할인
class PercentageDiscount implements DiscountStrategy {
    private double percentage;

    public PercentageDiscount(double percentage) {
        this.percentage = percentage;
    }

    public double applyDiscount(double price) {
        return price - (price * percentage / 100);
    }
}

// 구체적인 전략 클래스: 고정 금액 할인
class FixedAmountDiscount implements DiscountStrategy {
    private double amount;

    public FixedAmountDiscount(double amount) {
        this.amount = amount;
    }

    public double applyDiscount(double price) {
        return price - amount;
    }
}

// Context 클래스
class ShoppingCart {
    private DiscountStrategy discountStrategy; // 사용할 할인 전략

    public void setDiscountStrategy(DiscountStrategy discountStrategy) {
        this.discountStrategy = discountStrategy; // 할인 전략 설정
    }

    public double calculateTotalPrice(double price) {
        if (discountStrategy != null) {
            return discountStrategy.applyDiscount(price); // 할인 전략에 따라 총 가격 계산
        }
        return price; // 할인 전략이 없을 경우 원 가격 반환
    }
}

// 클라이언트 클래스
public class Client {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();

        // 정률 할인 전략 설정
        cart.setDiscountStrategy(new PercentageDiscount(10)); // 10% 할인
        double totalPrice1 = cart.calculateTotalPrice(100);
        System.out.println("Total price after percentage discount: " + totalPrice1); // 90.0 출력

        // 고정 금액 할인 전략 설정
        cart.setDiscountStrategy(new FixedAmountDiscount(20)); // 20 달러 할인
        double totalPrice2 = cart.calculateTotalPrice(100);
        System.out.println("Total price after fixed amount discount: " + totalPrice2); // 80.0 출력
    }
}

```

- **DiscountStrategy 인터페이스**
  - 할인 알고리즘을 정의하는 전략 인터페이스
  - `applyDiscount` 메서드를 포함하고 있다
- **ConcreteStrategy 클래스들**
  - 두 가지 구체적인 전략 클래스 `PercentageDiscount`(정률 할인)과 `FixedAmountDiscount`(고정 금액 할인)를 구현
  - 각 클래스는 `applyDiscount` 메서드를 통해 각기 다른 할인 방식의 로직을 제공한다
- **ShoppingCart 클래스 (Context)**
  - 사용자가 사용할 할인 전략을 설정하고, `calculateTotalPrice` 메서드를 통해 할인 적용 후의 가격을 계산한다
- **Client 클래스**
  - 클라이언트 코드는 `ShoppingCart` 객체를 생성하고, 다양한 할인 전략을 설정하여 가격을 계산한다

**전략 패턴 장점**

- **유연성**: 전략을 쉽게 교환할 수 있어, 클라이언트가 필요에 따라 다양한 알고리즘을 적용할 수 있다
- **캡슐화**: 알고리즘의 구현 세부 사항을 캡슐화하여 클라이언트 코드가 이를 신경 쓰지 않게 한다
- **코드 중복 감소**: 각 전략 클래스에서 공통된 로직을 재사용할 수 있어 코드의 중복을 줄일 수 있다

`@Repository` 어노테이션을 사용해 데이터베이스 접근 방식을 설계하는 것도 일종의 전략 패턴이다. 실제로 저번 자바 프로젝트에서 추후에 실제 db를 연결을 할 것까지 염두에 두고 개발을 했었는데 인터페이스도 만들어 두어서 다른 db접근 방식으로 해당 인터페이스를 구현한 후, 어노테이션을 기존 것(인메모리 레포지토리)에는 떼고 새로 만든 레포지토리에 붙이면 그렇게 작동 했을 것이다. 이런 설계또한 전략 패턴의 일종이라고 볼 수 있다.
