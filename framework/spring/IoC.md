# IoC(Inversion of Control)

IoC(Inversion of Control)은 제어의 역전으로 객체 지향 프로그래밍에서 중요한 설계 원칙 중 하나이다. 객체의 생성과 관리의 제어권을 개발자가 아닌 외부 프레임워크나 컨테이너에게 맡기는 방식이다. IoC는 일반적으로 객체간의 의존성을 관리하고 애플리케이션의 흐름을 제어하는 방식에서 개발자가 객체를 직접 생성하고 관리하는 대신 외부 시스템이 대신 객체를 관리하고 주입하는 방식으로 흐름을 변화시킨다

참고로, IoC는 스프링의 개념이 아닌 프레임워크의 설계 원칙이다

## 기본 개념

IoC는 객체의 생명주기와 의존성 관리를 외부에서 담당하는 설계 패턴이다. 이를 통해 애플리케이션은 **덜 결합**되고 더 유연하며 테스트하기 쉬운 구조로 변환된다.

### IoC를 사용한 흐름 (IoC 컨테이너가 제어하는 방식)

- 객체의 생명주기와 의존성 관리는 **IoC 컨테이너**(예: 스프링)가 담당한다. `UserService` 객체가 필요한 `UserRepository` 객체를 자동으로 주입받게 된다

```java
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) { // IoC 컨테이너가 객체를 주입
        this.userRepository = userRepository;
    }
}
```

위의 예에서처럼, **스프링**과 같은 IoC 컨테이너는 객체 간의 의존성을 자동으로 관리하고, 객체를 생성할 때 필요한 의존성을 주입해 준다. 해당 방식으로 객체의 생명주기 관리와 의존성 관리를 외부에서 처리하게 된다

## 종류

### 1. **DI(Dependency Injection)** - 의존성 주입

- DI는 IoC의 **구체적인 구현 방식** 중 하나로, 객체의 의존성 관계를 **외부에서 주입**하는 방법이다
- 객체가 다른 객체를 **직접 생성하거나 관리하지 않고**, **외부에서 필요한 의존 객체를 주입받는** 방식
- **스프링**에서 사용되는 방식이며, 생성자, 세터, 필드 주입 방식이 있다(위에서 확인 가능..)

### 2. **Service Locator**

- Service Locator는 **서비스를 찾을 수 있는 중앙 위치**를 두고, 필요한 객체를 요청하는 방식이다
- 애플리케이션에서 객체를 사용할 때, **Service Locator**가 해당 객체를 찾아서 반환한다
- 이 방식은 DI보다는 덜 유연하고, 의존성 추적이 어려운 단점이 있다. 따라서 스프링에서는 DI 방식을 더 많이 사용한다

## IoC와 DI

IoC는 제어의 역전이라는 큰 개념을 의미하고, DI는 IoC를 구현하기 위한 구현 방식 중 하나이다. IoC의 핵심은 객체 간의 의존 관계를 외부 시스템이 관리하도록 만드는 것이며 DI는 이를 의존성 주입을 통해 해결하는 방식이다

## 장점

1. 객체 간 결합도 감소
   1. IoC를 통해 객체 간의 의존 관계가 분리되어 결합도가 낮아진다
   2. 각 객체는 다른 객체를 생성하거나 관리하지 않고 외부에서 주입받기만 하면 되기 때문
2. 유연성
   1. IoC 컨테이너는 객체의 생명주기와 의존성 관리, 설정등을 담당하므로 객체를 교체하거나 변경할 때 코드 수정이 최소화된다
3. 확장성
   1. 새로운 객체나 의존성의 추가가 쉬워진다
   2. 기존의 객체를 수정하지 않고 새로운 객체를 쉽게 추가할 수 있다
4. 테스트 용이성
   1. IoC 사용시 객체 간의 의존성을 주입받기 때문에 테스트 시 실제 객체 대신 mock객체등을 사용하여 테스트가 가능하다

## 스프링의 IoC

스프링은 IoC의 가장 유명한 구현체 중 하나다! 스프링 IoC 컨테이너가 객체의 생명주기와 의존성을 관리한다. 스프링에서는 `@Component` , `@Controller` 등의 어노테이션을 통해 객체를 스프링 컨테이너에 등록하고 의존성 주입을 통해 객체간의 관계를 설정한다

- 스프링 IoC 컨테이너의 주요 기능
  - 빈 등록 및 관리
    - 스프링 IoC 컨테이너는 애플리케이션의 객체를 빈으로 등록하여 관리한다
  - 의존성 주입
    - 객체가 의존하는 다른 객체를 컨테이너에서 주입받는다
  - 빈의 생명 주기 관리
    - 빈의 생성, 초기화, 소멸 등의 생명 주기를 관리한다

추가적으로, 스프링 IoC 컨테이너는 `ApplicationContext` 인터페이스와 이를 구현한 다양한 클래스에 정의되어 있다. 스프링 애플리케이션은 주로 `ApplicationContext` 를 사용하여 빈을 관리하고 객체 간의 의존성을 주입한다.

주요 `ApplicationContext` 구현 클래스는 아래와 같다

1. **`AnnotationConfigApplicationContext`**

   - 자바 기반 설정을 사용하는 IoC 컨테이너입니다
   - 주로 `@Configuration`과 `@Bean` 어노테이션을 사용하여 빈을 설정할 때 활용
   - 자바 코드 기반의 설정을 통해 애플리케이션을 구성할 때 사용

   ```java
   ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
   ```

2. **`ClassPathXmlApplicationContext`**

   - XML 파일을 사용하여 스프링 설정을 정의하는 IoC 컨테이너
   - XML 파일을 통해 빈을 설정할 때 사용

   ```java
   ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
   ```

3. **`FileSystemXmlApplicationContext`**

   - 파일 시스템의 특정 경로에 있는 XML 설정 파일을 로드하여 IoC 컨테이너를 초기화`ClassPathXmlApplicationContext`와 비슷하지만 파일 시스템의 경로를 사용할 수 있다

   ```java
   ApplicationContext context = new FileSystemXmlApplicationContext("/path/to/applicationContext.xml");
   ```

4. **`WebApplicationContext`**
   - 웹 애플리케이션 환경에서 사용되는 IoC 컨테이너로 Spring MVC에서 서블릿과 함께 사용되며 `ApplicationContext`의 서브타입이다
   - `DispatcherServlet`에 의해 초기화되어 웹 애플리케이션에서 주로 사용된다

<aside>

`BeanFactory` 와의 관계
`ApplicationContext`는 `BeanFactory` 인터페이스를 확장하고 있어 `BeanFactory`가 제공하는 모든 기능을 포함하면서 추가적인 기능(예: 메시지 리소스 처리, 이벤트 시스템, 애플리케이션 레벨 설정 등)을 제공해준다. `BeanFactory`는 IoC 컨테이너의 가장 기본적인 형태이지만 실제로 스프링 애플리케이션에서는 주로 `ApplicationContext`가 사용된다

</aside>
