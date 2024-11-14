# AOP(Aspect-Oriented Programming)

AOP, 관점 지향 프로그래밍은 객체 지향 프로그래밍과 함께 사용하는 프로그래밍 패러다임으로 비지니스 로직과는 별개로 자주 사용되는 기능(주로 로깅, 트랜잭션, 보안 처리 등)을 애플리케이션의 여러 모듈에 쉽게 적용할 수 있도록 해준다. 이를 통해 코드의 중복을 줄이고 특정 기능을 여러 곳에서 쉽게 재사용할 수 있도록 돕는다

## 관심사 횡단이란?

관심사 횡단(Cross-Cutting Concerns)은 애플리케이션 전반에서 공통으로 필요하지만, 비즈니스 로직과 직접적인 관련이 없는 기능들을 의미한다. 일반적으로 애플리케이션의 여러 부분에 걸쳐 적용되어야 하며, 이러한 관심사를 효율적으로 관리하지 않으면 코드 중복이 발생하고 유지보수가 어려워진다. 대표적인 관심사 횡단 예시로는 **로깅, 보안, 트랜잭션 관리, 예외 처리** 등이 있다

→ 이를 AOP를 통해 해결 가능하다

AOP는 횡단 관심사를 애플리케이션의 핵심 비즈니스 로직과 분리하여 관리할 수 있도록 도와준다. 스프링 AOP에서는 비즈니스 로직 외부에 관심사 횡단 기능을 정의한 **Aspect**를 작성하고, 이를 특정 시점(Join Point)에서 적용할 수 있다

## 주요 개념

1. **Aspect (관점)**
   - 여러 객체에 공통으로 적용되는 기능을 정의한 모듈로 횡단 관심사를 캡슐화하는 단위이다
   - 예를 들어, 로깅 기능을 Aspect로 구현할 수 있다
2. **Advice (어드바이스)**
   - Aspect에서 실제로 실행되는 작업을 의미한다
   - Advice는 언제, 어떻게 특정 기능이 적용될지 정의한다
     - **Before** : 대상 메서드가 실행되기 전에 실행
     - **After** : 대상 메서드가 실행된 후에 실행
     - **AfterReturning** : 대상 메서드가 정상적으로 종료된 후에 실행
     - **AfterThrowing** : 대상 메서드가 예외를 던졌을 때 실행
     - **Around** : 대상 메서드 실행 전후에 실행, 메서드 실행을 완전히 제어 가능
3. **Pointcut (포인트컷)**
   - Advice가 적용될 지점을 정의하는 표현식이다
   - 특정 클래스의 특정 메서드에만 Advice를 적용하거나, 모든 메서드에 적용하는 등 매우 유연하게 지정할 수 있다
4. **JoinPoint (조인포인트)**
   - Advice가 적용될 수 있는 모든 지점
   - 스프링 AOP에서는 주로 메서드 호출 지점이 JoinPoint가 된다
5. **Weaving (위빙)**
   - Aspect를 실제 객체에 적용하는 과정을 의미한다
   - 스프링에서는 런타임 시 프록시를 생성하여 위빙을 수행한다

## 사용 예시

스프링에서는 AOP를 주로 **프록시 패턴**을 활용하여 구현한다

`@Aspect` 어노테이션을 사용하여 Aspect를 정의하고, `@Before`, `@After`, `@Around` 등으로 Advice를 구현한다

```java

@Aspect
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore() {
        System.out.println("Method execution started");
    }

    @AfterReturning("execution(* com.example.service.*.*(..))")
    public void logAfter() {
        System.out.println("Method execution finished");
    }

    @Around("execution(* com.example.service.*.*(..))")
    public Object logAround(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println("Before method execution");
        Object result = joinPoint.proceed(); // 실제 메서드 호출
        System.out.println("After method execution");
        return result;
    }
}
```

위는 AOP를 적용한 예시인데, `logBefore` 메서드는 메서드 실행 전에, `logAfter`는 실행 후에, `logAround`는 실행 전후에 호출된다. `execution(* com.example.service.*.*(..))`는 포인트컷 표현식으로, 특정 패키지에 있는 모든 메서드를 대상으로 한다는 의미이다

## 장점

- **중복 코드 제거** : 공통 기능을 하나의 Aspect로 관리하여 중복 코드를 줄인다
- **모듈화** : 횡단 관심사를 모듈화하여 코드 가독성을 높이고 유지보수성을 향상시킨다
- **유연성** : 특정 기능을 애플리케이션의 여러 모듈에 쉽게 추가할 수 있다

## 사용 사례

- **로깅** : 모든 메서드 호출 전후에 로깅 기능을 적용
- **트랜잭션 관리** : 데이터베이스 작업에 트랜잭션을 쉽게 적용
- **보안** : 접근 권한이 필요한 메서드에 접근 제어를 적용
