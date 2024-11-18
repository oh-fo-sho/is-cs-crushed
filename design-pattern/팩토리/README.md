### 팩토리 패턴

팩토리 패턴은 객체 생성에 대한 인터페이스를 정의하고 어떤 클래스의 인스턴스를 생성할지는 서브클래스에서 결정하게 하는 패턴이다. 해당 패턴은 객체 생성을 클라이언트 코드에서 분리시켜 코드의 유연성을 높이는 데에 목적이 있다.

팩토리 패턴은 크게 두가지로 분류 가능하다

1. 팩토리 메서드 패턴
   1. 서브 클래스에서 객체 생성을 담당
2. 추상 팩토리 패턴
   1. 관련된 객체들을 생성하기 위한 인터페이스를 제공하고 실제 생성은 서브클래스에서 처리하는 패턴

```java
interface Product {
	void use();
}

class AnyProductA implements Product {
	public void use() {
		sout("using anyProductA");
	}
}

class AnyProductB implements Product {
	public void use() {
		sout("using anyProductB");
	}
}

// 팩토리 인터페이스
abstract class Creator {
    public abstract Product factoryMethod(); // 팩토리 메서드

    public void someOperation() {
        Product product = factoryMethod(); // 팩토리 메서드를 사용하여 제품 생성
        product.use();
    }
}

class ConcreteCreatorA extends Creator {
    public Product factoryMethod() {
        return new AnyProductA();
    }
}

class ConcreteCreatorB extends Creator {
    public Product factoryMethod() {
        return new AnyProductB();
    }
}

public class Main {
    public static void main(String[] args) {
        Creator creatorA = new ConcreteCreatorA();
        creatorA.someOperation(); // AnyProductA 사용

        Creator creatorB = new ConcreteCreatorB();
        creatorB.someOperation(); // AnyProductB 사용
    }
}
```

- **Product 인터페이스**
  - 생성될 제품의 인터페이스를 정의
- **AnyProductA, AnyProductB**
  - `Product` 인터페이스를 구현한 구체적인 제품 클래스
- **Creator 클래스**
  - 팩토리 메서드를 정의하는 추상 클래스
  - `factoryMethod()` 메서드는 서브클래스에서 구현해야 한다
- **ConcreteCreatorA, ConcreteCreatorB**
  - `Creator` 클래스를 상속받아 `factoryMethod()` 메서드를 구현하여 각각 `AnyProductA`와 `AnyProductB`를 생성
- **Main 클래스**
  - `Creator` 객체를 생성하고, `someOperation()` 메서드를 호출하여 제품을 사용

→ 메인에서는 어떤 클래스의 인스턴스가 생성되는지 알 필요 없이 `Creator` 인터페이스를 통해 제품을 사용할 수 있게 하는 것이다. 이로 인해 클라이언트 단에서 직접 인스턴스를 생성하지 않았고, 서브 클래스에서 인스턴스가 실제로 생성됨을 확인 할 수 있다
