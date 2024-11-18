## 주요 어노테이션

### AnnotationConfigApplicationContext

- `AnnotationConfigApplicationContext` 는 스프링 컨테이너 중하나로 자바 기반의 설정 클래스에서 빈을 읽어 관리하는 컨테이너이다!
- 이 컨테이너는 `@Configuration` 과 `@Bean` 으로 정의된 설정을 기반으로 애플리케이션의 빈을 생성하고 관리하며 싱글톤으로 관리할 수 있도록 지원한다
- 주요 기능
  - 빈 등록 및 검색 : 등록된 빈을 이름 혹은 타입으로 검색할 수 있다
  - 싱글톤 관리 : 각각의 빈은 기본적으로 싱글톤 스코프로 관리되어 애플리케이션 내에서 동일한 인스턴스로 사용된다
  - 라이프사이클 관리 : 빈의 생성 및 소멸 라이프사이클을 관리하며 초기화와 종료 시점에 필요한 콜백 메서드도 지원한다.

### `@Configuration`

- 스프링 설정 클래스임을 나타내는 어노테이션
- 해당 어노테이션이 붙은 클래스는 하나 이상의 `@Bean` 메서드를 정의하며 스프링의 IoC 컨테이너는 이 클래스의 인스턴스를 빈 설정의 원천으로 사용한다
- DB 설정이나 특정 빈을 직접 생성해서 관리해야 하는 경우 `@Configuration` 을 통해 설정 가능

### `@Bean`

- 메서드 수준에서 사용, 해당 **메서드의 리턴 객체를** 스프링 컨테이너에서 관리하는 빈으로 등록
- `@Configuration` 이 붙은 클래스에서 주로 사용하며 `@Bean` 메서드를 통해 생성된 객체는 싱글톤으로 관리됨
- 외부 API 클라이언트나 DB 연결 객체처럼 **직접 생성하여 스프링에서 관리하고 싶은 객체**를 `@Bean` 으로 정의

### `@SpringBootApplication`

`@SpringBootApplication` 은 스프링 부트 애플리케이션의 진입점 클래스에 붙이는 어노테이션으로 스프링 부트의 핵심 설정을 포함한 메타 어노테이션이다. `@SpringBootApplication` 에는 세가지 어노테이션이 포함되어 있다

1. `@Configuration`
   1. 이 클래스가 스프링 설정 클래스임을 나타냄
   2. 애플리케이션의 설정 및 빈 정의가 포함됨
2. `@EnableAutoConfiguration`
   1. 스프링 부트의 자동 설정 기능을 활성화
   2. 스프링 부트가 클래스패스와 여러 설정을 기반으로 필요한 빈을 자동으로 구성하도록 도와줌
3. `@ComponentScan`
   1. 스프링 부트 애플리케이션이 있는 기본 패키지와 하위 패키지를 스캔하여 `@Component` 가 붙은 클래스를 자동으로 빈으로 등록

**특징**

- `@SpringBootApplication` 이 붙은 클래스는 기본적으로 애플리케이션의 시작점으로 간주
- 스프링 부트는 이 클래스의 위치를 기준으로 **동일한 패키지와 하위 패키지를 자동으로 스캔하여** 빈으로 등록
- `@EnableAutoConfiguation` 으로 자동 설정이 활성화 되어 다양한 스프링 기능을 사용하도록 해줌

### `@ComponentScan`

`@ComponentScan`은 **지정된 패키지와 그 하위 패키지에 있는 클래스들을 스캔하여** 스프링 컨테이너에 빈으로 등록하는 어노테이션이다. 이 어노테이션은 `@Component` , `@Controller` , `@Service` , `@Repository` 와 같은 어노테이션이 붙은 클래스를 자동으로 검색하고 빈으로 등록해준다

**특징**

- 기본적으로 **`@ComponentScan`을 사용하는 클래스의 패키지**와 그 하위 패키지를 스캔
- 스캔할 패키지를 직접 지정할 수도 있고 여러 개의 패키지를 지정하는 것도 가능
- `basePackages` 속성을 통해 **특정 패키지에서만 스캔**하도록 설정할 수 있다

### `@Component`

- 스프링의 자동 감지 기능을 통해 등록되는 빈을 정의하는 어노테이션
- 컨트롤러, 서비스, 리포지토리 처럼 명확한 역할이 없는 일반적인 빈에 사용
- `@Component` 가 붙은 클래스는 스프링 컨테이너가 스캔하여 자동으로 빈으로 등록

### `@Controller`

- `@Component` 의 파생 어노테이션
- 스프링 MVC의 컨트롤러 역할을 하는 클래스에 사용
- http 요청을 받고 적절한 서비스 호출, 응답을 반환하는 역할
- `@Controller` 가 붙은 클래스는 요청 경로와 매핑하여 클라이언트 요청을 처리할 수 있도록 스프링 MVC 컨텍스트에 등록됨

### `@Service`

- `@Component` 의 파생 어노테이션
- 비지니스 로직을 담당하는 서비스 계층 클래스에 사용
- 주로 `@Controller` 나 다른 서비스에서 호출되며 데이터 처리 및 비지니스 로직 수행
- 이 어노테이션은 주로 개발자가 해당 클래스의 역할을 인식기 위한 명명적 어노테이션
  - 별 다른 기능이 없다

### `@Repository`

- `@Component` 의 파생 어노테이션
- 데이터 액세스 계층에 사용
- 데이터베이스와의 통신 담당, sql 실행, jpa 사용 등을 포함한 데이터 액세스 로직 구현
- 주로 예외 처리와 관련된 부가 기능이 포함되어 있다
  - 데이터 접근 시 발생하는 특정 예외를 처리할 수 있도록 도와줌

### 객체 생성 순서와 관련된 어노테이션

스프링에서는 의존성 주입 과정에서 어떤 빈을 사용할 지 결정할 때 `@Primary` 와 `@Qualifier` 어노테이션을 사용하여 객체 생성과 관련된 순서를 제어할 수 있다. 이는 특히 하나의 타입에 여러 빈이 있을 경우 특정한 빈을 우선 선택하거나 명시적으로 지정하여 주입받을 수 있도록 한다

1. `@Primary`

   `@Primary`는 동일한 타입의 여러 빈이 존재할 때, 해당 빈을 기본적으로 우선하여 주입받도록 설정하는 어노테이션이다. `@Primary`를 통해 특정 빈을 우선시하면, 다른 빈 이름을 명시하지 않더라도 우선적으로 선택한다

2. `@Qualifier`

   `@Qualifier`는 동일한 타입의 여러 빈이 있을 때, 특정 빈을 명시적으로 지정하여 주입받고자 할 때 사용하는 어노테이션이다. `@Qualifier`는 `@Primary`보다 더 강력한 우선순위를 가지므로 `@Primary`가 선언된 빈이 있어도 `@Qualifier`로 특정 빈을 명시하면 해당 빈이 주입된다

3. `@Order`

   `@Order`는 빈 생성 순서나 실행 순서를 지정할 때 사용된다. 예를 들어 여러 빈을 특정 순서대로 실행하거나 처리해야 할 때 사용된다. `@Order`는 일반적으로 `@Component`나 `@Bean` 메서드 위에 사용되며 낮은 숫자일수록 우선순위가 높다

   ### 인터페이스나 추상 클래스로 주입 시, 구현체가 여러개면?

   인터페이스 타입으로 빈을 주입할 때 스프링 컨텍스트에 해당 인터페이스를 구현한 빈이 여러개일 경우 스프링은 어느 구현체를 주입할 지 모호해져서 예외가 발생할 수 있다. 해당 문제는 빈의 우선순위를 지정하거나 특정 빈을 명시적으로 선택하는 방법으로 해결 가능하다

   1. 순서를 지정하지 않았을 때 → 예외 발생
   2. 순서 지정방법
      1. `@Primary` 사용하기
      2. `@Qualifier` 로 특정 빈 지정하기
      3. `@Order` 로 빈의 순서 지정하기 (컬렉션으로 주입할 경우)