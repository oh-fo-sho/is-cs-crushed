# Tomcat

## 역할

스프링 부트에는 톰캣이 내장 되어 있는데, 내장 톰캣 서버 시작시 지정된 포트(8080)를 열고 HTTP 요청을 받을 준비를 하게된다

1. 포트 열기
   1. 스프링 애플리케이션이 시작되면 내장 톰켓 서버가 설정된 포트로 TCP 소켓을 연다. 해당 포트는 `application.properties` 혹은 `application.yml` 에서 `server.port` 설정으로 지정 가능하다
      1. 기본값은 8080
2. 클라이언트와의 TCP 3-Way 핸드셰이크
   1. 외부 클라이언트가 서버에 접속하려고 하면 TCP 연결을 설정하기 위해 3-Way 핸드셰이크 수행
   2. 해당 과정에서 클라이언트가 서버로 `SYN` 패킷을 보내고 서버는 응답으로 `SYN-ACK` 패킷을 보낸다. 이후 클라이언트는 `ACK` 패킷을 보내 연결이 확립됨
3. HTTP 통신
   1. TCP 연결이 설정되면 클라이언트는 HTTP 프로토콜을 사용해 요청을 보냄
   2. 톰캣 서버는 클라이언트가 전송한 HTTP 요청을 수신하고 이를 스프링 애플리케이션으로 전달한다
   3. 스프링은 컨트롤러에서 요청을 처리하고 HTTP 응답을 생성시켜 톰캣에 반환한다
   4. 톰캣은 생성된 HTTP 응답을 TCP 연결을 통해 클라이언트로 전송

## 스레드 풀링

Tomcat의 스레드 풀링은 서블릿 컨테이너가 다수의 요청을 효율적으로 처리하기 위한 기법으로 요청이 올 때마다 새로운 스레드를 생성하는 대신 미리 스레드를 만들어 풀이 저장해 두고 재사용하는 방식을 말한다. 이러한 방법으로 스레드 생성과 제거에 따른 자원 낭비를 줄이고 처리 성능을 향상시킬 수 있다

### 스레드 풀 개념

- 스레드 풀은 일정 수의 스레드를 미리! 생성해 두고 클라이언트의 요청이 있을 때마다 해당 스레드 풀에서 스레드를 할당해 요청을 처리한다
- 요청 처리 이후에 스레드는 다시 풀로 돌아가 다음 요청을 기다린다. 이렇게 처리하게 되면 매 요청마다 스레드를 생성하고 제거하는 비용을 절감한다

### 톰캣의 스레드 풀 구성 요소

- 최소 스레드 수
  - 톰캣이 시작될 때 기본적으로 생성하는 최소 스레드 수
- 최대 스레드 수
  - 스레드 풀에서 유지 가능한 최대 스레드 수
  - 기본값 200
- 대기 큐
  - 최대 스레드의 수만큼 스레드가 이미 사용 중인 경우 추가 요청을 대기하는 큐
  - 기본값 100
  - 이 대기 큐도 차면 서버는 더 이상의 요청을 수락하지 않고 연결 거부
- 작업 완료 후 대기 시가
  - 각 스레드는 설정된 대기 시간 동안 다음 요청 기다림, 요청 없을 시 종료

### 톰캣 스레드 풀 동작 방식

1. 요청 수락
   1. 클라이언트 요청시 톰캣은 스레드 풀에서 사용 가능한 스레드 찾아 요청 처리
2. 스레드 재사용
   1. 요청이 끝난 스레드는 스레드 풀로 돌아가 다음 요청 기다림
3. 스레드 생성 및 제거
   1. 요청이 많아져 사용 중인 스레드가 최대 스레드 수에 도달할 때까지 새로운 스레드 생성
   2. 요청이 줄어들 경우 일부 스레드는 keepAliveTime 이후에 종료
4. 부하 분산 및 처리 속도 향상
   1. 스레드 풀 덕분에 여러 요청이 동시에 처리됨
   2. 스레드 생성과 소멸에 따른 부하를 줄여 서버 응답 속도 개선됨

### 멀티스레딩 환경에서 동일한 데이터 접근시??

스프링 부트의 내장 톰캣은 멀티스레드 환경에서 요청을 처리하기 때문에 여러 스레드가 동시에 동일한 데이터에 접근할 수 있는 상황이 발생 가능하다. 이를 해결하기 위한 원칙과 기술이 사용된다

1. **Stateless Design (무상태 설계)**
   1. HTTP 요청-응답 모델은 본질적으로 무상태, 요청마다 상태를 유지하지 않고 독립적으로 처리되도록 설계, 고로 각 요청은 별도의 스레드에서 독립적으로 처리되고 요청 간에 공유된 상태를 가지지 않도록 한다
   2. 스프링의 `@Controller` 나 `@Service` 같은 빈 객체는 싱글톤으로 관리되지만 상태를 공유하지 않기 때문에 스레드에 안전하다!
   3. **상태 없는 설계를** 통해 멀티스레드 환경에서도 안전하게 동작하도록 해야한다
      1. 인스턴스 변수 사용 X
2. **Thread Safety (스레드 안전성)**
   1. 여러 스레드가 동시에 접근하는 경우 동시성 문제가 발생할 수 있기에 공유 데이터를 사용하는 곳에서는 스레드 안전성을 보장해야한다
      1. 지역 변수 사용 → 메서드 내의 지역 변수는 각 스레드가 독립적으로 사용하므로 공유되지 않아 스레드 안전을 보장 가능
      2. 불변 객체 → 여러 스레드가 접근해도 변경되지 않는 불변 객체를 사용하면 동시성 문제 회피 가능
      3. 동기화 → 꼭 필요한 경우 `synchronized` 키워드나 `Lock` 객체로 동기화 시킴, 다만 성능에 악영향
3. **Request Scope와 ThreadLocal**
   1. RequestScope
      1. 각 요청마다 새로운 빈이 생성되어 사용되는 스코프
      2. `@RequestScope` 로 설정된 빈은 요청 단위로 새로운 인스턴스 생성 시켜 다른 스레드와 격리됨
   2. ThreadLocal
      1. 스레드가 요청시 특정 데이터만 접근하도록 격리하는 방법
      2. `ThreadLocal` 사용 시 스레드가 고유한 데이터 가질 수 있다
      3. 메모리 누수 가능성 있기에 반드시 사용 후 `remove()` 해야함
4. **데이터베이스 트랜잭션과 동시성 제어**
   1. 트랜잭션이 필요한 서비스에서는 데이터베이스 수준에서 동시성 제어를 통해 데이터의 일관성 보장
   2. 트랜잭션 시작시, 데이터베이스는 다른 스레드의 변경이 영향을 주지 않도록 잠금을 걸거나 격리 수준 설정하여 문제 방지
   3. 스프링의 `@Transactional` 을 사용해 서비스 계층에서 트랜잭션 관리하면 멀티스레드 상황에서 데이터의 일관성 유지 가능

> **불변 객체란?**

- 모든 필드를 `fianl` 로 선언
- 필드를 `private` 으로 선언하고 외부에 노출하지 않음
- 가변 객체를 필드로 사용시 깊은 복사
- 상태 변경 메서드 제공하지 않기
  >

## 서블릿 컨테이너

서블릿 컨테이너는 서블릿을 관리하고 톰캣이 해당 역할을 수행한다

1. 서블릿 컨테이너의 역할
   1. 서블릿 컨테이너는 서블릿의 생명주기(초기화, 요청 처리, 종료)를 관리하고 HTTP 요청을 적절한 서블릿으로 **라우팅하는 역할**을 한다
   2. 톰캣이 대표적인 서블릿 컨테이너, 서블릿을 생성, 초기화, 요청에 대한 응답을 처리하고 서블릿의 자원을 해제하는 모든 과정을 담당한다
2. 서블릿 관리
   1. 톰캣은 서블릿을 적절하게 로드하고 초기화하여 HTTP 요청이 들어올 때 서블릿의 `service()` 메서드를 호출하여 요청을 처리하게 한다
   2. 서블릿은 클라이언트 요청마다 새로 생성되지 않고 싱글톤으로 관리되어 재사용된다(멀티스레드로 요청 처리)
   3. 이를 통해 성능을 높이고 매 요청마다 서블릿을 새로 생성하지 않도록 한다
3. 요청 객체 파싱
   1. 톰캣은 HTTP 요청을 수신하면 요청을 분석하고 파싱하여 `HttpServletRequest` 와 `HttpServletResponse` 객체를 생성한다
   2. 이 객체들은 클라이언트로부터 받은 http 헤더, 파라미터, 쿠키 및 세션 등의 정보를 포함하고 서블릿에 전달되어 요청 정보를 쉽게 접근할 수 있게 한다

# Servlet

## Servlet의 문제점

1. 복잡한 코드 관리
   1. 서블릿은 html 코드와 자바 비지니스 로직이 함께 있어 코드가 복잡해진다. 하나의 서블릿 클래스 안에 html을 출력하는 코드와 요청 처리를 위한 비지니스 로직이 함께 존재하면 유지보수와 확장이 어려움
2. 재사용성 부족
   1. 서블릿 자체에는 비지니스 로직을 컴포넌트화하고 재사용을 위한 구조가 부족
3. http 요청과 응답에 대한 복잡한 처리
   1. 서블릿은 http get, post, put, delete 등과 같은 요청 메서드 처리 방식이 비효율적이다 모든 요청을 `doGet()` , `doPost()` 등의 메서드에서 처리해야 하며 요청마다 분기처리 하는 코드가 복잡해짐
4. 의존성 관리
   1. 서블릿 안에서 모든 의존성을 직접 생성하고 관리해야 하므로 다양한 모듈 간 의존성을 관리하기 어려움

<aside>

스프링 프레임워크에서는 이러한 서블릿의 문제를 해결하기 위해 MVC 아키텍처, 의존성 주입, 트랜잭션 관리, AOP 지원 등을 제공한다. 스프링은 비지니스 로직과 뷰를 명확히 분리하고 테스트 용이성을 높이며 다양한 부가 기능을 모듈화하여 확장성과 유연성을 크게 향상 시켰다

</aside>