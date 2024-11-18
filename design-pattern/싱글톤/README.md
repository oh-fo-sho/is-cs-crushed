### 싱글톤 패턴

싱글톤 패턴은 특정한 클래스의 인스턴스가 하나만 생성되게 하고 해당 인스턴스에 대한 전역적인 접근점을 제공하는 패턴이다. 주로 애플리케이션 전체에서 하나의 인스턴스만 필요한 경우에 사용된다. 데이터베이스 연결이나 설정 관리 등이 있을 것이다.

> **주요 특징** > **1. 유일한 인스턴스 2. 전역 접근 3. Lazy Initialization : 필요할 때! 인스턴스를 생성하는 방식으로 메모리 자원을 아낄 수 있다**

```java
public class Single {
	private static Single instance;

	private Single() {
		// 외부에서 이 생성자로 생성하지 못하게 private으로 두는게 포인트
	}

	public static Single getInstance() {
		// 필요할 때 만들어 주는게 Lasy Initialization이다
		if (instance == null) {
			instance = new Single();
		}
		return instance;
	}
```

```java
public class Main {
	public static void main(String[] args) {
		Single single = Single.getInstance();
		// Single single1 = new Single(); 이 방법으로 생성이 불가능
	}
}
```

- **private static Singleton instance**
  - 클래스의 유일한 인스턴스를 저장할 정적 변수를 선언
- **private Singleton()**
  - 생성자를 private으로 설정하여 외부에서 인스턴스를 생성할 수 없도록 한다
  - 전역적으로 객체의 생성 접근점을 하나만 두는 것
- **getInstance()**
  - 인스턴스가 null인 경우에만 새로 생성하고, 이미 존재하는 경우 그 인스턴스를 반환한다
  - 이로 인해 인스턴스가 하나만 존재하도록 보장한다

<aside>
💡

**주의점**
지금 사용한 방식은 스레드 안전성을 보장하지 않는다

고로 멀티스레드 환경에서 사용할 경우 동기화(synchronized)를 고려해야한다

</aside>
