# DI

DI(Dependency Injection)은 객체지향 프로그래밍에서 중요한 개념 중 하나로 **의존성 주입을** 통해 객체 간의 의존관계를 해결하는 방식을 의미한다. DI는 객체가 다른 객체에 의존하지 않고 외부에서 주입받는 형태로 의존 관계를 관리하는 방식으로 애플리케이션의 유연성, 확장성, 테스트 용이성을 높여준다!

## 기본 개념

DI는 한 객체가 다른 객체를 생성하거나 관리하는 것이 아니라 **필요한 의존 객체를 외부에서 주입받는 방식**이다 객체 간의 의존성을 객체 내부에서 해결하는 것이 아닌 외부에서 미리 해결하고 객체는 의존성을 주입받아 사용하게 된다

```java
public class DemoService {
    private DemoRepository demoRepository;

    // DI를 통해 UserRepository 객체를 주입받음
    public DemoService(UserRepository demoRepository) {
        this.demoRepository = demoRepository;
    }

    public void performService() {
        demoRepository.save();
    }
}
```

이런 코드에서 `DemoService` 는 `DemoRepository` 객체에 의존하고 있다. 하지만 `DemoService` 는 `DemoRepository` 를 직접 생성하지 않고 외부에서 주입받는다 이렇게 외부에서 주입하는 방식이 DI이다

## 장점

- 유연성
  - 의존 객체를 쉽게 교체 가능
  - 데이터베이스와의 연동을 변경하더라도 `DemoService` 는 아무 수정 없이 다른 Repository 객체를 주입 받으면 된다
- 테스트 용이성
  - 의존성 주입을 통해 테스트용 객체(ex: Mock)를 쉽게 주입 가능해 단위 테스트가 쉬워진다.
- 확장성
  - 객체가 독립적으로 설계되므로 시스템 확장 혹은 변경시 각 객체를 수정할 필요없고 새로운 의존 객체만 추가하면 된다
- 중복 코드 감소
  - 객체 생성과 관련된 중복 코드를 줄일 수 있다

## DI의 종류

1. **생성자 주입(Constructor Injection)**

   1. 의존성을 생성자를 통해 주입하는 방식
   2. 객체가 생성될 때 필요한 의존 객체를 생성자에서 주입받는다
   3. 주로 불변 객체를 생성할 때 유용하고 의존성의 필수성을 강제 할 수 있다

   ```java
   public class UserService {
       private final UserRepository userRepository;

       public UserService(UserRepository userRepository) {
           this.userRepository = userRepository;
       }
   }
   ```

2. **세터 주입(Setter Injection)**

   1. 의존성을 세터 메서드에서 주입하는 방식
   2. 객체를 생성하고 세터 메서드를 호출하여 의존 객체 설정
   3. 선택적인 의존성 설정시 유용
      1. 실행되다가 의존성을 바꿀거 같을때 사용, 분명한 이유없이 잘 안씀

   ```java
   public class UserService {
       private UserRepository userRepository;

       public void setUserRepository(UserRepository userRepository) {
           this.userRepository = userRepository;
       }
   }
   ```

3. **필드 주입(Field Injection)**
   1. 의존성을 필드에 직접 주입하는 방식
   2. `@Autowired` 어노테이션을 사용하여 스프링이 의존성을 자동으로 주입
   3. 주로 자동 의존성 주입에 사용되므로 테스트와 리팩토링에 어려움이 있다
      1. 현재는 안티 패턴

## DI의 구현 방식

DI는 스프링과 같은 DI 컨테이너를 활용하여 구현 가능하다. 스프링은 객체 생성, 의존성 관리 등을 자동으로 처리해 주며 빈으로 등록된 객체들은 필요한 곳에 자동으로 주입된다

```java
@Component
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

- `@Component` 어노테이션은 `UserService` 클래스를 스프링 빈으로 등록
- `@Autowired`는 생성자에 주입할 객체를 자동으로 주입하도록 함

## DI와 IoC

DI는 IoC의 한 형태이다. IoC는 프로그램의 흐름 제어권을 개발자가 아닌 외부 프레임워크가 담당하도록 하는 디자인 패턴이다. 객체 생성과 의존성 관리의 제어권을 스프링 컨테이너가 관리하도록 하여 개발자는 더 이상 객체 간의 관계 설정을 코드로 직접 처리할 필요가 없게 된다

## DI와 AOP

DI는 AOP와 결합하여 사용된다. 스프링에서는 DI를 사용하여 객체를 생성하고 AOP를 사용하여 해당하는 객체의 횡단 관심사를 적용할 수 있다. 이때 AOP는 객체의 로직에 영향을 주지 않고 객체의 동작을 보조하는 방식으로 작동한다
