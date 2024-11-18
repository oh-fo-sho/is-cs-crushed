### 프록시 패턴

프록시 패턴은 다른 객체에 대한 접근을 제어하는 대리 객체를 사용하는 디자인 패턴이다. 해당 패턴은 주로 클라이언트가 실제 객체에 접근하기 전에 추가적인 기능을 제공하기 위해 사용한다. 프록시는 실제 객체의 역할을 수행하거나 접근을 제어, 지연 로딩, 로깅, 권한 확인 등의 기능을 구현할 수 있다.

프록시 패턴의 주 목적은 클라이언트와 실제 객체 간의 연결을 관리하고 실제 객체에 대한 간접적인 접근을 가능하게 하는 것이다.

> **프록시 패턴의 구성 요소**

1. 인터페이스 (Subject) : 실제 객체와 프록시가 모두 구현하는 공통 인터페이스
2. 리얼 서브젝트 (RealSubject) : 실제 작업을 하는 클래스
3. 프록시 (Proxy) : 리얼 서브젝트에 대한 접근을 제어하는 클래스, 요청을 리얼 서브젝트에 전달하거나 추가적인 작업을 수행하는 역할
   >

```java
interface Subject {
    void request();
}

// 실제 객체 클래스
class RealSubject implements Subject {
    public void request() {
        System.out.println("RealSubject: Handling request.");
    }
}

// 프록시 클래스
class Proxy implements Subject {
    private RealSubject realSubject;

    public void request() {
        // 프록시가 요청을 처리하기 전에 추가 작업을 수행할 수 있음
        if (realSubject == null) {
            realSubject = new RealSubject(); // 지연 로딩
        }

        // 요청을 실제 객체에 전달
        preRequest();
        realSubject.request();
        postRequest();
    }

    private void preRequest() {
        System.out.println("Proxy: Pre-processing request.");
    }

    private void postRequest() {
        System.out.println("Proxy: Post-processing request.");
    }
}

// 클라이언트 클래스
public class Client {
    public static void main(String[] args) {
        Subject proxy = new Proxy(); // 프록시 객체 생성
        proxy.request(); // 프록시를 통해 요청
    }
}

```

- **Subject 인터페이스**
  - 클라이언트가 사용할 수 있는 인터페이스
  - `request()` 메서드를 정의
- **RealSubject 클래스**
  - 실제 작업을 수행하는 클래스
  - `request()` 메서드를 구현하여 클라이언트의 요청을 처리한ㄷ
- **Proxy 클래스**
  - `Subject` 인터페이스를 구현하며, `RealSubject` 인스턴스를 포함한다
  - `request()` 메서드에서 요청을 처리하기 전후에 추가 작업을 수행하고, 실제 요청은 `RealSubject`에 전달한다
- **Client 클래스**
  - 프록시 객체를 생성하고, 이를 통해 클라이언트가 요청을 보낸다

<aside>
💡

**프록시 패턴의 장점!**

- **접근 제어** 리얼 서브젝트에 대한 접근을 제어하여 보안 및 권한 검사를 추가할 수 있다
- **지연 로딩** 리얼 서브젝트의 생성 비용이 클 경우, 실제 필요할 때까지 객체 생성을 지연시킬 수 있다
- **로깅 및 모니터링** 요청 전후에 로깅이나 모니터링 작업을 추가하여 시스템 상태를 추적할 수 있다
</aside>

자바나 객체지향에 대한 관점은 아니지만 React에서도 프록시의 개념이 사용된다. React의 상태 관리 라이브러리 중 하나인 MobX(주류는 redux이지만..)는 프록시를 사용하여 상태 변경을 감지하고 UI를 업데이트 시킨다. MobX가 객체를 프록시로 감싸고 상태가 변경될 때 이를 감지하여 실제로 반응형 UI를 구현하는 것이다.

또 다른 프록시 개념 중 하나는 프록시 서버가 있다. 프록시 서버는 클라이언트와 서버 사이에 끼어서 중개 역할을 맡는다. 프록시 서버의 기능으로는 요청 및 응답을 조작할 수 있고, cors 문제(다른 도메인에 요청할 때 발생하는 문제)를 해결할때 cors 제한을 우회하는 방식으로 해결 가능하고, 로드 밸런싱(서버에 대한 요청을 분산 처리하여 부하를 낮춤), 캐싱(클라이언트에서 자주 요청한 응답을 캐시해서 서버 부하를 낮춤)등으로 사용되기도 하는.. 아주 중요한 패턴이다
