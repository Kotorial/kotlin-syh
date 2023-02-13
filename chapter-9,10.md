## 9장 테스트

### 여러 데이터에 JUnit5 테스트 반복하기

- 데이터 값 세트를 제공하여 JUnit5 테스트 실행하고 싶다

### 파라미터화된 테스트에 data 클래스 사용하기

- 파라미터화된 테스트를 좀 더 쉽게 읽는 테스트 결과를 생성하고 싶다
- 입력 값과 예상 값을 감싸는 data 클래스를 만들고, 만든 data 클래스 기반의 테스트 데이터를 생성하는 함수를 테스트 메소드 소스로서 사용한다

```kotlin
data class FibonacciTestData(val number: Int, val expected: Int)
```

```kotlin
@ParameterizedTest
@MethodSource("fibonacciTestData")
fun `asd`(data: FibonacciTestData) {
	assertThat(fibonacci(data.number), `is`(data.expected))
}

private fun fibonacciTestData() = Stream.of(
	FibonacciTestData(number = 1, expected = 1),
...
)
```

- 앞의 파라미터화되니 테스트를 좀 더 읽기 쉽게 만든 것

## 10장

### use로 리소스 관리하기

- 파일 같은 리소스를 처리하고 사용을 끝마쳤을 때 확실하게 리소스를 닫고 싶지만 코틀린은 자바의 try-with-resources 구문을 지원하지 않는다
- 자바에서 `Closeable`을 구현한 클래스라면 try 블록 안의 리소스를 JVM 블록이 시작할 때 자동으로 열어주고, 끝날때 자동으로 닫아준다
- 코틀린은 try-with-resources 구조는 지원하지 않는다
- 대신 `Closable` 에 확장함수 use, Reader를 File에는 useLine을 추가했다

```kotlin
inline fun <T> File.useLines(
	charset: Charset = Charsets.UTF_8,
	block: (Sequence<String>) -> T
): T
```

```kotlin
fun get10LongestWordsInDictionary() = 
	File("/use/share/dict/words").useLines { line -> 
		line.filter { it.length > 20 }
			.sortedByDescending(String::length)
			.take(10)
			.toList()
}
```

```kotlin
// 코틀린의 useLines 구현
inline fun <T> File.useLines(
	charset: Charset = Charsets.URF_8,
	block: (Sequence<String>) -> T
): T = bufferedReader(charset).use { block(it.lineSequence()) }
```

```kotlin
// 위의 use의 시그니쳐
inline fun <T: Closeable?, R> T.use(block: (T)->R): R
```
