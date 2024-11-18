# Lambda

## 익명 클래스

익명 클래스는 이름 없는 클래스 객체를 생성하여 한 번에 정의하고 인스턴스를 생성하는 방식을 말한다. 보통 인터페이스나 추상 클래스의 메서드를 간단하게 구현할 때 사용한다.

## 람다 표현식

람다 표현식은 함수형 인터페이스(메서드가 하나만 있는 인터페이스)의 인스턴스를 단순하고 간결하게 표현하기 위해 사용한다.

| 특징           | 익명 클래스                  | 람다 표현식                                 |
| -------------- | ---------------------------- | ------------------------------------------- |
| 문법           | `new Interface() {...}` 형식 | `(param) -> expression` 형식                |
| 클래스 생성    | 새로운 클래스 생성           | 추가 클래스 생성 없음                       |
| 구현 범위      | 여러 메서드 구현 가능        | 함수형 인터페이스의 단일 메서드만 구현 가능 |
| this 키워드    | 익명 클래스 자신 참조        | 외부 객체 참조                              |
| 사용 가능 버전 | Java 1.1 이상                | Java 8 이상                                 |

Java의 함수형 인터페이스는 람다 표현식과 자주 사용되고 Java 8에서 도입된 `java.util.function` 패키지에 포함되어 있다. 자주 사용되는 인터페이스들인 **Consumer, Supplier, Function, Predicate, Operator, Runnable**에 대해 알아보자

## Consumer (소비자)

`Consumer<T>`는 주어진 객체를 받아서 어떤 동작을 수행하지만 값을 반환하지 않는 함수형 인터페이스이다. "소비"만 하고 결과는 반환하지 않는다

- 메서드 시그니처
  - `void accept(T t)`
- 용도
  - 출력, 로깅, 데이터 기반으로 다른 작업 수행시 사용

```java
Consumer<String> printConsumer = s -> System.out.println(s);
printConsumer.accept("Hello, Consumer!");
```

## Supplier (공급자)

`Supplier<T>`는 입력 없이 어떤 값을 생성하여 반환하는 함수형 인터페이스이다. "공급" 역할을 하며, 매번 호출할 때마다 값을 반환한다

- 메서드 시그니처
  - `T get()`
- 용도
  - 객체 생성이나 객체 반환시 사용

```java
Supplier<Double> randomSupplier = () -> Math.random();
System.out.println(randomSupplier.get()); // 랜덤 숫자 출력
```

## Function (함수)

`Function<T, R>`는 입력을 받아서 결과를 반환하는 함수형 인터페이스이다. `apply` 메서드를 통해 입력 타입 `T`를 받아 출력 타입 `R`을 반환한다

- 메서드 시그니처
  - `R apply(T t)`
- 용도
  - 값을 변환하거나 매핑할 때 사용

```java
Function<String, Integer> lengthFunction = s -> s.length();
System.out.println(lengthFunction.apply("Hello")); // 문자열 길이 5 출력
```

## Predicate (조건자)

`Predicate<T>`는 입력값을 평가하여 `true` 또는 `false`를 반환하는 함수형 인터페이스, 주로 조건식을 간단히 표현할 때 사용한다

- 메서드 시그니처
  - `boolean test(T t)`
- 용도
  - 필터링 조건, 검색 조건 등으로 사용

```java
Predicate<Integer> isEven = n -> n % 2 == 0;
System.out.println(isEven.test(4)); // true
System.out.println(isEven.test(5)); // false
```

## Operator (연산자)

`Operator` 인터페이스는 같은 타입의 입력을 받아서 같은 타입의 출력을 반환하는 함수형 인터페이스이다. `UnaryOperator<T>`와 `BinaryOperator<T>` 두 가지가 있으며, 각각 인수 하나와 인수 두 개를 받아 연산을 수행한다.

- 메서드 시그니처 (UnaryOperator)
  - `T apply(T t)`
- 메서드 시그니처 (BinaryOperator)
  - `T apply(T t1, T t2)`
- 용도
  - 수학 연산이나 데이터 변경에 많이 사용됨

```java
UnaryOperator<Integer> square = n -> n * n;
System.out.println(square.apply(5)); // 25 출력
```

```java
BinaryOperator<Integer> add = (a, b) -> a + b;
System.out.println(add.apply(5, 3)); // 8 출력
```

## Runnable

`Runnable`은 `java.lang` 패키지에 포함된 함수형 인터페이스로 아무 인자도 받지 않고 아무 값도 반환하지 않으며 작업을 수행하기만 한다. `run` 메서드만 포함한다

- 메서드 시그니처
  - `void run()`
- 용도
  - 멀티스레딩 작업을 정의하거나 간다히 실행만 해야할 코드를 정의할 때 사용

```java
Runnable runnable = () -> System.out.println("Hello from Runnable!");
new Thread(runnable).start(); // 별도의 스레드에서 실행
```
