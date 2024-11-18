# Controller & View

Spring MVC에서 `DispatcherServlet` 과 `ViewResolver` 는 클라이언트 요청을 처리하고 적절한 응답을 생성하는 역할을 맡는다. 이들의 역할과 동작 순서를 알아보자

## Dispatcher Servlet

`DispatcherServlet` 은 Spring MVC에서 제공하는 서블릿이고 java 서블릿의 구현체 중 하나이다

- 서블릿은 Java EE(Servlet API)에서 제공하는 표준 인터페이스이고 Http 요청과 응답을 처리하는 기본 구성 요소이다
- DispatcherServlet은 이 서블릿 인터페이스를 확장하여 Spring MVC의 요청과 응답 처리 흐름을 구현한 것이다

### 톰캣과의 관계

톰캣은 Java 서블릿 컨테이너이고 서블릿을 실행하는 환경을 제공한다. DispatcherServlet도 서블릿이기 때문에 톰캣이 이를 관리한다

- 서블릿 생명주기(생성, 초기화, 요청, 처리, 종료) 등을 제어
- Http 요청을 톰캣이 먼저 받아서 DispatcherServlet으로 전달

톰캣이 요청을 DispatcherServlet에 전달할 경우 요청 정보도 포함된다

- Query String
- http body 데이터
- 요청 헤더, 쿠키 등의 모든 데이터를 `HttpServletRequest` 객체에 담아 DispatcherServlet에 전달하게 되는 것

### 스프링에서는 DispatcherServlet만 사용하나?

**SpringMVC에서는 기본적으로 DispatcherServlet만 사용한다**

SpringMVC는 모든 요청을 DispatcherServlet이 처리하도록 설계되어 있다. DispatcherServlet은 프론트 컨트롤러로 모든 http 요청을 받아 Spring 애플리케이션의 적절한 처리 흐름으로 전달한다

하지만, 다른 서블릿도 사용은 가능하다. 직접 `HttpServlet` 을 구현하고 스프링 부트에서 추가 서블릿을 등록해주어야 한다.

### 역할

`DispatcherServlet` 은 Spring MVC에서 프론트 컨트롤러로 동작한다. 모든 http 요청은 먼저 DispatcherServlet으로 전달된다

1. 요청을 처리할 컨트롤러 검색
   1. 요청 url 과 등록된 핸들러 매핑을 기반으로 컨트롤러를 찾는다
2. 컨트롤러 호출
   1. 컨트롤러 메서드를 실행하고 결과를 반환받는다
   2. 컨트롤러는 ModelAndView 나 다른 응답을 DispatcherServlet에 반환한다
3. 컨트롤러의 결과 처리
   1. 반환된 view 이름을 viewResolver에 전달하여 뷰를 찾음
   2. 뷰를 렌더링하고 클라이언트에 응답을 전달함

## View Resolver

`ViewResolver` 는 컨트롤러에서 반환된 뷰 이름을 기반으로 적절한 View 객체를 찾아주는 역할을 한다

### 역할

1. 뷰 이름 처리
   1. 컨트롤러가 `hello` 라는 이름의 뷰 이름을 반환하면 ViewResolver는 이를 사용해 실제 뷰 파일을 결정한다
   2. 뷰 이름과 실제 파일 경로를 매핑한다
2. View 객체 반환
   1. 뷰 이름을 기반으로 적합한 View 객체를 생성
   2. View객체는 렌더링할 데이터를 포함하고 클라이언트에 보낼 html을 생성

### 호출 시점

ViewResolver는 컨트롤러가 뷰 이름을 반환하면 DispatcherServlet에서 호출이 된다. 정확히는 컨트롤러가 `ModelAndView` 혹은 뷰 이름을 반환하면 DispatcherServlet은 뷰 이름을 ViewResolver에 넘기고 적합한 View를 찾는 것

## 동작 순서

1. 클라이언트 요청
   1. 사용자가 브라우저에서 `/hello` 로 요청을 보냄
2. DispatcherServlet
   1. 요청을 처리하기 위해 적합한 컨트롤러를 찾음
   2. `HandlerMapping` 을 통해 요청과 매핑된 컨트롤러 메서드를 호출 시킴
3. 컨트롤러 실행

   ```java
   @Controller
   public class ExampleController {
       @GetMapping("/hello")
       public String example(Model model) {
           model.addAttribute("key", "value");
           return "hello";
       }
   }
   ```

   1. 컨트롤러 메서드가 실행되고 뷰 이름을 포함하는 ModelAndView 객체를 반환

4. DispatcherServlet
   1. 반환된 `hello` 라는 뷰 이름을 ViewResolver에 전달
5. VeiwResolver 호출
   1. `ViewResolver` 가 `hello` 를 실제 파일 경로로 변환하고 적절한 뷰 객체를 반환
6. 뷰 렌더링
   1. View 객체가 렌더링을 수행하여 클라이언트에 html, json 등 응답을 반환

## `@ResponseBody`

Spring MVC에서 `@ResponseBody` 는 http 응답 본문에 직접 데이터를 쓰는 데에 사용된다. 이 과정에서 HttpMessageConverter가 핵심역할을 맡는다

### 동작 과정

1. 컨트롤러에서 반환된 데이터를 확인
   1. 반환된 값의 타입과 요청의 `Accept` 헤더를 분석
2. 적절한 HttpMessageConverter 선택
   1. Spring은 등록된 여러 `HttpMessageConverter` 중 요청과 응답 조건에 맞는 것을 하나 선택
3. 변환 후 Http 응답 생성
   1. 선택된 `HttpMessageConverter` 가 반환 값을 변환하여 응답 본문에 작성

## StringHttpMessageConverter

StringHttpMessageConverter는 컨트롤러가 String 타입을 반환할 때 사용되는 `HttpMessageConverter` 이다

- String 반환
  - 컨트롤러가 반환한 Stirng 값을 HTTP 응답 본문에 그대로 작성한다
- UTF-8 인코딩
  - 응답 데이터의 기본 문자 셋을 UTF-8로 사용
    - 이렇게 동작하기 때문에 한글을 입력하고 charset을 기재해주지 않아도 깨지지 않고 출력 가능
  - 만약 요청 헤더에 명시된 게 있다면 이를 따른다

```java
@GetMapping("/hello")
@ResponseBody
public String sayHello() {
    return "Hello World!";
}
```

- 반환 값 `String` → `StringHttpMessageConverter` 가 처리
- 최종 응답 본문
  ```
  Hello, World!
  ```
- 응답 헤더
  ```
  Content-Type: text/plain;charset=UTF-8
  ```

## **MappingJackson2MessageConverter**

**MappingJackson2HttpMessageConverter**는 컨트롤러가 **객체(Object) 타입**을 반환할 때 사용된다. 주로 JSON 형식으로 데이터를 변환한다

- Jackson 라이브러리 사용
  - Jackson ObjectMapper를 이용해 자바 객체를 JSON으로 직렬화(serialize)하거나 역직렬화(deserialize) 한다
- Content-type 자동 설정
  - 반환 데이터를 JSON으로 변환하고 응답 헤더의 `Content-Type` 을 `application/json` 으로 설정한다
