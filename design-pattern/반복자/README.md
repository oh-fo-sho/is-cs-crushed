### 반복자 패턴

반복자 패턴은 컬렉션 객체의 내부 구조를 노출하지 않고도 그 요소들을 순차적으로 접근할 수 있도록 하는 행동 디자인 패턴이다. 해당 패턴은 데이터 구조와 알고리즘을 분리시켜 컬렉션을 직접 조작하지 않고도 요소를 순회할 수 있는 방법을 제공한다.

> **반복자 패턴 구성 요소**
>
> - **Iterator**: 컬렉션의 요소를 순회하기 위한 인터페이스, 일반적으로 `next()`, `hasNext()` 등의 메서드를 포함한다
> - **ConcreteIterator**: 실제로 요소를 순회하는 클래스로, Iterator 인터페이스를 구현합니다. 내부 상태를 유지하여 현재 위치를 추적한다
> - **Aggregate**: 반복자를 생성하기 위한 인터페이스, 이 인터페이스는 반복자를 반환하는 메서드를 포함한다
> - **ConcreteAggregate**: 실제 데이터 구조를 가진 클래스, 이 클래스는 반복자 객체를 생성하는 메서드를 구현한다

```java
// 반복자 인터페이스
interface Iterator {
    boolean hasNext(); // 다음 요소가 있는지 확인
    Object next(); // 다음 요소를 반환
}

// 집합체 인터페이스
interface Aggregate {
    Iterator createIterator(); // 반복자 생성 메서드
}

// 실제 반복자 클래스
class ConcreteIterator implements Iterator {
    private ConcreteAggregate aggregate; // 참조할 집합체
    private int index; // 현재 인덱스

    public ConcreteIterator(ConcreteAggregate aggregate) {
        this.aggregate = aggregate;
    }

    public boolean hasNext() {
        return index < aggregate.getSize(); // 인덱스가 집합체의 크기보다 작은지 확인
    }

    public Object next() {
        return aggregate.getElementAt(index++); // 현재 인덱스의 요소를 반환하고 인덱스 증가
    }
}

// 실제 집합체 클래스
class ConcreteAggregate implements Aggregate {
    private Object[] items; // 요소를 저장할 배열
    private int size;

    public ConcreteAggregate(int capacity) {
        items = new Object[capacity];
        size = 0;
    }

    public void add(Object item) {
        if (size < items.length) {
            items[size++] = item; // 요소 추가
        }
    }

    public int getSize() {
        return size;
    }

    public Object getElementAt(int index) {
        return items[index];
    }

    public Iterator createIterator() {
        return new ConcreteIterator(this);
    }
}

// 클라이언트 클래스
public class Client {
    public static void main(String[] args) {
        ConcreteAggregate aggregate = new ConcreteAggregate(5);
        aggregate.add("Item 1");
        aggregate.add("Item 2");
        aggregate.add("Item 3");

        Iterator iterator = aggregate.createIterator();

        while (iterator.hasNext()) {
            System.out.println(iterator.next()); // 반복자 사용하여 요소 출력
        }
    }
}

```

- **Iterator 인터페이스**
  - 컬렉션의 요소를 순회하는 메서드를 정의
- **Aggregate 인터페이스**
  - 반복자를 생성하는 메서드를 포함함
- **ConcreteIterator 클래스**
  - `Iterator` 인터페이스를 구현하여, 현재 인덱스를 추적하고 다음 요소를 반환하는 기능을 제공함
- **ConcreteAggregate 클래스**
  - `Aggregate` 인터페이스를 구현하며, 요소를 저장하는 배열을 가지고 있습니다. 반복자를 생성하는 메서드도 포함되어 있다
- **Client 클래스**
  - `ConcreteAggregate` 객체를 생성하고 요소를 추가한 후, 반복자를 통해 요소를 순회하고 출력한다

**반복자 패턴의 장점**

1. 데이터 구조와 알고리즘의 분리
   1. 반복자 패턴을 사용하면 데이터 구조를 직접 노출하지 않고도 그 요소를 순회할 수 있습니다. 이로 인해 데이터 구조가 변경되어도 클라이언트 코드는 영향을 받지 않는다
2. 다양한 데이터 구조에 적용 가능
   1. 리스트, 세트, 맵 등 다양한 데이터 구조에 반복자 패턴을 적용할 수 있어, 통일된 방식으로 요소를 순회할 수 있다
3. 복잡한 순회 로직 숨김
   1. 내부 순회 로직을 캡슐화하여 클라이언트 코드가 단순해진다
