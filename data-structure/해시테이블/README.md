# HashMap

해시맵은 **키-값(key-value)** 쌍으로 데이터를 저장하는 해시 기반의 컬렉션. 해시맵은 효율적인 데이터 접근을 가능하게 하고 데이터 검색, 삽입 및 삭제를 빠르게 수행할 수 있다

### 특징

- **키-값 쌍 저장**
  - 각 값은 고유한 키에 연결되어 저장
- **해시 함수 사용**
  - 해시맵은 내부적으로 해시 함수를 사용하여 키를 해시값으로 변환하여 배열의 인덱스를 계산
  - 이를 통해 빠른 검색이 가능
- **중복 키 허용 안 함**
  - 해시맵은 중복된 키를 허용하지 않음
  - 동일한 키로 값을 저장하면 이전 값이 새로운 값으로 대체
- **순서 없음**
  - 해시맵에 저장된 요소들은 순서를 보장하지 않음
  - 저장한 순서대로 요소가 반환되지 않음
- **null 값 허용**
  - 해시맵에서는 키 또는 값에 `null`을 허용
  - 그러나 키는 오직 하나만 `null`일 수 있다

### **주요 연산**

- **put(key, value)**: 키와 값을 해시맵에 추가
- **get(key)**: 키에 해당하는 값을 반환
- **remove(key)**: 특정 키에 대한 값을 제거
- **containsKey(key)**: 특정 키가 해시맵에 존재하는지 확인
- **size()**: 해시맵에 저장된 요소의 개수를 반환
- **clear()**: 해시맵의 모든 요소를 제거

### **해시맵의 장단점**

### **장점**

- **빠른 검색**: 평균적으로 O(1)의 시간 복잡도로 검색할 수 있어 매우 빠름
- **유연성**: 다양한 타입의 키와 값을 사용 가능
- **다양한 데이터 구조**: 복잡한 데이터 구조를 쉽게 표현 가능

### **단점**

- **순서 없음**: 요소가 저장된 순서를 유지X
- **메모리 사용량**: 해시맵은 해시 테이블을 기반으로 하므로 상대적으로 메모리 사용이 많을 수 있음
- **동기화 필요**: 멀티스레드 환경에서 사용할 경우 동기화가 필요
  - hashtable이 이를 지원하지만 이제 레거시라 사용 X
  - `concurrent hashmap` 사용이 권장된다

## 해시맵 사용 예제

```java
import java.util.HashMap;

public class HashMapExample {
    public static void main(String[] args) {
        // 해시맵 생성
        HashMap<String, Integer> map = new HashMap<>();

        // 요소 추가 (put)
        map.put("Alice", 30);
        map.put("Bob", 25);
        map.put("Charlie", 35);

        System.out.println("HashMap after additions: " + map); // {Alice=30, Bob=25, Charlie=35}

        // 요소 조회 (get)
        int ageOfBob = map.get("Bob");
        System.out.println("Bob's age: " + ageOfBob); // 25

        // 요소 제거 (remove)
        map.remove("Charlie");
        System.out.println("HashMap after removing Charlie: " + map); // {Alice=30, Bob=25}

        // 키 존재 여부 확인 (containsKey)
        boolean hasAlice = map.containsKey("Alice");
        System.out.println("Does the map contain Alice? " + hasAlice); // true

        // 해시맵 크기 확인 (size)
        int size = map.size();
        System.out.println("Size of the HashMap: " + size); // 2

        // 해시맵 비우기 (clear)
        map.clear();
        System.out.println("HashMap after clearing: " + map); // {}
    }
}
```

## 해시 함수

해시 함수란 입력값을 고정된 크기의 해시 값으로 반환하는 함수를 일컫는다. 데이터의 빠른 검색 및 데이터의 무결성을 보장하기 위해 사용한다. 해시 함수는 다양한 분야에서 활용된다.

### 특징

- **고정 길이 출력**
  - 해시 함수는 입력 데이터의 크기와 상관없이 항상 동일한 길이의 해시 값을 생성한다
  - 예를 들어, SHA-256 해시 함수는 어떤 입력이든 항상 256비트(32바이트) 길이의 출력을 제공함
- **일방향성(비가역성)**
  - 해시 함수는 주어진 입력으로부터 해시 값을 쉽게 계산할 수 있음
  - 해시 값으로부터 원래의 입력을 역산하는 것은 매우 어려움
  - 보안에서 중요한 특성
- **충돌 저항성**
  - 서로 다른 입력이 동일한 해시 값을 생성하는 경우를 충돌(collision)이라고 한다. 좋은 해시 함수는 가능한 한 충돌을 피해야 하며 충돌이 발생하더라도 이들을 식별하는 것이 어려워야 한다

## 모듈러 연산

모듈러 연산은 수학에서 특정 수를 다른 수로 나눈 나머지를 구하는 연산이다. 일반적으로 "a를 m으로 나눈 나머지"라고 표현하고 이를 `a mod m` 으로 나타낸다. 여기서 `a` 는 피제수, `m` 은 모듈로 라고한다.
