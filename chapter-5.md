## 5.1 배열 다루기

- `arrayof`를 사용하여 배열을 만들고, Array 클래스에 들어 있는 속성과 메소드를 이용해 배열에 들어 있는 값을 다룬다

```kotlin
val strings = arrayOf("this", "is", "sparta")
val nullStringArray = arrayOfNulls<String>(5) // null로 이루어진 배열도 생성 가능
```

- Array 클래스는 public 생성자 하나만 있다
- Int 타입의 size, `(int) → T` 타입의 람다 둘만 인자로 받는다

```kotlin
val squares = Array(5) { i -> (i * i).toString() } // 0, 1, 3, ..
```

- 오토박싱과 언박식 비용을 줄이기 위해 기본 타입으로  배열을 만드는 함수도 제공한다
    - `booleanArrayOf, byteArrayOf` 등
    - 코틀린에는 기본 타입이 없지만, 널 비허용인 경우 생성된 바이트코드는 기본값을 사용한다고 한다

- 배열의 인덱스 값을 알고 싶다면 Array의 `indicies` 속성을 사용하면 된다

```java
val Strings = arrayOf("asd", "asd")
val indices = strings.indices
```

### 5.2 컬렉션 생성하기

- `listOf, setOf, mapOf` 처럼 변경 불가능한 컬렉션을 생성하기 위해 만들어진 함수나
- `mutableListOf, mutableSetOf, mutableMapOf` 처럼 변경 가능한 컬렉션을 생성하기 위해 고안된 함수도 제공한다

- kotlin.collections 패키지가 제공하는 유틸 함수를 통해 컬렉션의 변경 불가능한 view를 얻을 수 있다
    - `listOf, setOf, mapOf` 등등
    
    ```java
    var numList = listOf(3, 1, 4, 2)
    ```
    
- List, Set, Map 인터페이스를 직접 구현한 클래스의 인스턴스도 생성할 수 있다

```java
val list = LinkedList<Int> ()
list.add(3)
list[0] = 3
```
