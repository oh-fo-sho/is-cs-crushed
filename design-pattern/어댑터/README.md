### 어댑터 패턴

**어댑터 패턴은** 서로 호환되지 않는 인터페이스를 가진 객체들이 협업할 수 있도록 하는 구조적 디자인 패턴이다. 해당 패턴은 클라이언트가 기대하는 인터페이스와 실제 구현 클래스 사이에서 나오는 불일치를 해결하고 클라이언트가 해당 클래스를 사용할 수 있도록 한다.

어댑터 패턴의 주 목적은 기존 클래스의 인터페이스를 변경하지 않고도 새로운 인터페이스와의 호환성을 제공하는 것이다. 이로 인해 기존 코드의 변경점을 줄일 수 있어 유연성과 재사용성을 높일 수 있다.

> **어댑터 패턴의 구성 요소**

1. 타겟 : 클라이언트가 사용하는 인터페이스
2. 어댑터 : 타겟 인터페이스를 구현, 어댑터가 연결할 실제 클래스를 포함함
3. 어댑티 : 호환이 안되는 기존 클래스
4. 클라이언트 : 타겟 인터페이스를 사용하여 어댑터를 통해 어댑티를 사용하게 되는 객체
   >

```java
// 타겟 인터페이스
interface Target {
    void request();
}

// 어댑티 인터페이스
class Adaptee {
    void specificRequest() {
        System.out.println("Specific request from Adaptee");
    }
}

// 어댑터 클래스
class Adapter implements Target {
    private Adaptee adaptee;

    public Adapter(Adaptee adaptee) {
        this.adaptee = adaptee;
    }

    public void request() {
        // 어댑티의 특정 요청을 호출
        adaptee.specificRequest();
    }
}

// 클라이언트 클래스
public class Client {
    public static void main(String[] args) {
        Adaptee adaptee = new Adaptee(); // 어댑티 인스턴스 생성
        Target target = new Adapter(adaptee); // 어댑터 인스턴스 생성

        // 클라이언트가 타겟 인터페이스를 통해 요청
        target.request(); // Adaptee의 특정 요청을 호출
    }
}
```

- **Target 인터페이스**
  - 클라이언트가 사용할 인터페이스
  - `request()` 메서드를 정의
- **Adaptee 클래스**
  - 기존의 호환되지 않는 클래스
  - `specificRequest()` 메서드 보유
- **Adapter 클래스**
  - `Target` 인터페이스를 구현하고, `Adaptee` 인스턴스를 포함한다
  - `request()` 메서드에서 `specificRequest()` 메서드를 호출하여 클라이언트가 요구하는 방식으로 어댑티의 기능을 제공한다
- **Client 클래스**
  - 어댑티 인스턴스를 생성하고 어댑터를 통해 클라이언트가 요청을 보낼 수 있도록 설정한다

실제로 만약 어댑터 패턴을 써야하는 경우라면 객체의 관점은 아니지만, xml로만 작동되는 A서버가 있다고 가정하고 json으로만 작동하는 B서버가 있다고 생각했을 때, xml을 json으로 바꾸어 전달해주는 서버 C를 만든다면 이 역시도 어댑터 패턴을 사용하게 된 것이다.
