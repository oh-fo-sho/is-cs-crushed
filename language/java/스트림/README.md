# Stream 🔥

오늘 스트림을 직접 구현해보는 좋은 경험을 얻었다. 스트림의 주요 특징인 메서드 체이닝을 위해 플루언트 인터페이스를 사용하고 스트림에서 자기 자신(스트림)을 리턴하도록 하는 구조는 생각해보면 당연하다. 스트림을 다른 언어에서도 많이 써봤기 때문에(굉장한 함수형 프로그래밍 언어는 아니지만 파이썬의 람다식, 리스트 컴프리헨션도 비슷한 개념같고.. 자바스크립트에서도 map, filter 등등은 자주 사용하기때문에) 간단한 구조의 스트림은 이해하기 어렵진 않았는데 단 한번도 직접 구현해봐야겠다는 생각은 못했는데 운이 좋은것 같습니다.

```java
package datastructure.stream;

import datastructure.collection.List;

import java.util.function.Consumer;
import java.util.function.Function;
import java.util.function.Predicate;

public interface Stream<T> {

    <R> Stream<R> map(Function<? super T, ? extends R> mapper);
    Stream<T> filter(Predicate<? super T> predicate);
    void forEach(Consumer<? super T> consumer);
    List<T> toList();

}

```

`Stream<T>` 인터페이스는 스트림에서 제공하는 기본적인 작업을 정의한다. 제네릭 타입 `T`를 받아들이며, 컬렉션의 요소들에 대해 아래와 같은 작업 수행이 가능하다

- **`map(Function<? super T, ? extends R> mapper)`**
  - 각 요소에 `mapper` 함수를 적용하여 새로운 스트림을 반환
- **`filter(Predicate<? super T> predicate)`**
  - 각 요소를 `predicate`에 따라 필터링한 후, 조건에 맞는 요소들로 새로운 스트림을 반환
- **`forEach(Consumer<? super T> consumer)`**
  - 각 요소에 대해 주어진 `consumer`를 실행
- **`toList()`**
  - 스트림의 모든 요소를 `List<T>`로 변환

```java
package datastructure.stream;

import datastructure.collection.Collection;
import datastructure.collection.arrayList.ArrayList;
import datastructure.collection.linkedList.LinkedList;
import datastructure.collection.queue.Queue;
import datastructure.collection.stack.Stack;

import java.util.Iterator;
import java.util.function.Consumer;

public abstract class AbstractStream<T> implements Stream<T> {

    protected final Collection<T> collection;
    protected abstract Iterator<T> iterator();

    public AbstractStream(Collection<T> collection) {
        this.collection = collection;
    }

    @Override
    public void forEach(Consumer<? super T> action) {
        Iterator<T> iterator = iterator();
        while ( iterator.hasNext() ) {
            action.accept(iterator.next());
        }
    }

    protected <R> Collection<R> getCollection() {
        if ( this.collection instanceof LinkedList<?> ) {
            return new LinkedList<>();
        } else if ( this.collection instanceof Stack<?>) {
            return new Stack<>();
        } else if ( this.collection instanceof Queue<?>) {
            return new Queue<>();
        } else {
            return new ArrayList<>();
        }
    }
}

```

`AbstractStream<T>`는 `Stream<T>`를 구현한 추상 클래스이다. 이 클래스는 `Stream`의 기본 동작을 구현하며, 주로 컬렉션을 처리하는 데 필요한 공통 기능을 제공한다

- **`collection` 필드**
  - `Collection<T>`를 protected 멤버로 갖고 있으며(상속됐을 사용 가능), 스트림이 작업할 컬렉션을 의미함
- **`iterator()` 메서드**
  - 추상 메서드로, 컬렉션의 반복자를 반환
  - 이 메서드는 각 구현 클래스에서 구체적으로 정의할 예정 → 추상 클래스라서 바로 구현 안해도 됨
- **`forEach(Consumer<? super T> action)`**
  - `Stream` 인터페이스의 `forEach` 메서드를 구현하며, 컬렉션의 각 요소에 대해 `action`을 실행
- **`getCollection()` 메서드**
  - 컬렉션이 어떤 타입인지 확인하여 새로운 컬렉션 타입을 반환하는 유틸리티 메서드
  - `LinkedList`, `Stack`, `Queue`, `ArrayList` 중 적절한 타입을 선택하여 반환

```java
package datastructure.stream;

import datastructure.collection.Collection;
import datastructure.collection.List;
import datastructure.collection.arrayList.ArrayList;

import java.util.Iterator;
import java.util.function.Function;
import java.util.function.Predicate;

public class ListStream<T> extends AbstractStream<T> {

    public ListStream(Collection<T> collection) {
        super(collection);
    }

    @Override
    protected Iterator<T> iterator() {
        return this.collection.iterator();
    }

    @Override
    public <R> Stream<R> map(Function<? super T, ? extends R> mapper) {

        Collection<R> result = getCollection();

        forEach(el -> result.add(mapper.apply(el)));

        return new ListStream<>(result);
    }

    @Override
    public Stream<T> filter(Predicate<? super T> predicate) {

        Iterator<T> iterator = iterator();
        Collection<T> result = getCollection();

        while ( iterator.hasNext() ) {
            T item = iterator.next();
            if ( predicate.test(item) ) {
                result.add(item);
            }
        }

        return new ListStream<>(result);
    }

    @Override
    public List<T> toList() {
        List<T> list = new ArrayList<>();

        Iterator<T> iterator = iterator();

        while( iterator.hasNext() ) {
            T item = iterator.next();
            list.add(item);
        }

        return list;
    }
}

```

`ListStream<T>`는 `AbstractStream<T>`를 상속한 클래스이다 `ListStream`은 `List<T>` 타입의 컬렉션에 대해 동작하는 스트림을 구현했다

- **`iterator()` 메서드**
  - `ListStream`에 대해서 `collection.iterator()`를 호출하여 `Iterator<T>`를 반환한다
- **`map(Function<? super T, ? extends R> mapper)`**
  - 컬렉션의 각 요소에 대해 `mapper` 함수를 적용하고, 그 결과로 새로운 컬렉션을 반환하는 메서드이다
  - 여기서 반환되는 새로운 컬렉션은 `ListStream`
- **`filter(Predicate<? super T> predicate)`**
  - 컬렉션의 각 요소에 대해 `predicate` 조건을 적용하여 필터링하고, 그 결과로 새로운 `ListStream`을 반환
- **`toList()` 메서드**
  - 스트림의 모든 요소를 `List<T>`로 변환하여 반환합니다.

```java
package datastructure.stream;

import datastructure.collection.Collection;

public class QueueStream<T> extends ListStream<T> {

    public QueueStream(Collection<T> collection) {
        super(collection);
    }
}

```

```java
package datastructure.stream;

import datastructure.collection.Collection;

public class StackStream<T> extends ListStream<T> {

    public StackStream(Collection<T> collection) {
        super(collection);
    }
}

```

Java 스트림은 주로 데이터의 배열, 컬렉션이나 입력 소스를 처리하는데 사용된다. 실제 Java 스트림 구조를 살펴보자

### 1. Stream 구조의 주요 특징

- **순차적(Sequential)과 병렬적(Parallel) 스트림**
  - Java 스트림은 기본적으로 순차적으로 동작하지만 `parallel` 메서드도 지원하여 병렬 스트림 생성도 가능하다 → 여러 스레드를 사용한 병렬 처리도 가능하다
- **지연 평가(Lazy Evaluation)**
  - 즉시 데이터를 처리하지 않고 `종료 연산` 이 실행될 때까지 계산을 지연시킴
  - `filter()` 나 `map()` 같은 중간 연산은 데이터를 즉시 처리하지 않고 종료 연산인 `forEach()` 나 `collect()` 가 호출되어야 연산 수행
- **불변성(Immutable)**
  - 스트림의 요소들은 변경 불가능
  - 스트림을 통해 데이터를 수정하는 것이 각 연산이 새로운 스트림을 변환하는 방식으로 데이터를 변형시킴

### 2. Stream의 구성 요소

1. **중간 연산(Intermediate Operations)**
   1. 중간 연산은 스트림을 변환하는 작업
   2. 스트림 자체를 변형하지 않고 항상 새로운 스트림 반환시킴
   3. 중간 연산은 지연 평가, 실제로 데이터 처리 x
   4. 예시
      1. `map()` : 각 요소에 함수를 적용하여 새로운 스트림 만듦
      2. `filter()` : 주어진 조건에 맞는 요소만 걸러내어 새로운 스트림 만듦
      3. `distinct()` : 중복된 요소 제거
      4. `sorted()` : 요소들을 정렬
2. **종료 연산(Terminal Operations)**
   1. 종료 연산은 스트림의 계산을 마치고 결과를 반환하는 연산
   2. 종료 연산 호출 시 스트림에 대한 모든 중간 연산 수행
   3. 예시
      1. `forEach()` : 스트림의 각 요소에 대해 지정된 작업을 실행
      2. `collect()` : 스트림의 요소들을 컬렉션으로 수집
      3. `reduce()` : 스트림의 모든 요소를 하나로 결합하여 단일 결과를 생성
      4. `count()` : 스트림에 포함된 요소의 개수를 반환
      5. `anyMatch()`, `allMatch()`, `noneMatch()` : 조건에 맞는 요소가 있는지 여부를 확인

### 3. Stream의 구조 예시

실제 `java.util.stream.Stream` 클래스는 내부적으로 연속적인 연산을 처리하는 **파이프라인** 구조를 가진다. 이 파이프라인은 중간 연산과 종료 연산으로 이루어져 있으며, 각 연산은 스트림 객체에 대해 체이닝 방식으로 연결되어있다.

- 실제 스트림 실행 예시

```java
List<String> list = Arrays.asList("apple", "banana", "cherry", "date");
long count = list.stream()
                 .filter(s -> s.startsWith("a"))
                 .map(String::toUpperCase)
                 .count();
```

1. `stream()` : `list`에서 스트림을 생성
2. `filter(s -> s.startsWith("a"))` : "a"로 시작하는 문자열만 필터링
3. `map(String::toUpperCase)` : 필터링된 문자열을 대문자로 변환
4. `count()` : 최종적으로 결과 개수를 반환

**→ 이 과정은 지연 평가 방식으로 동작하며, `count()`가 호출될 때까지 중간 연산(`filter()`, `map()`)은 실제로 실행되지 않는다**

### 4. Stream 내부 구현

Java의 `Stream`은 내부적으로 **연결된 노드들**처럼 동작하는 파이프라인을 구성할 수 있다. 각 노드는 중간 연산을 담당하고, 스트림을 통해 데이터가 흐를 때 이 연산들이 순차적으로 적용된다

**기본적인 구현 구조**

- **Stream**은 데이터 소스에서 시작됩니다(예: `List`, `Array` 등)
- **중간 연산**은 변환, 필터링, 정렬 등을 수행
  - 연산이 체인처럼 연결되어 데이터 흐름을 만듭니다
- **종료 연산**이 호출되면 데이터 흐름을 마무리하고, 최종 결과를 반환

**내부 데이터 구조**

- **데이터 소스**
  - 스트림이 데이터를 읽어오는 원본 컬렉션(예: `List`, `Set`, `Array`)
- **파이프라인**
  - 중간 연산들이 결합된 구조로, 각 연산이 순차적으로 처리된다
- **최종 결과**
  - 종료 연산을 통해 결과가 생성

<aside>
💡

**스트림은 노드들이 연결된 파이프라인**이라고 볼 수 있다.

- 각 연산은 하나의 노드로 볼수 있고 노드 간 데이터가 흐른다
- 중간 연산들은 변환이나 필터링을하고 새로운 스트림을 반환하여 다음 연산으로 이어지는 파이프라인 만듦
- 종료 연산은 파이프라인은 끝내는 연산, 그때까지의 모든 중간 연산 실행
</aside>
