## 4장 함수형 프로그래밍

### 레시피 4.1 알고리즘에서 `fold` 사용하기

- 반복 알고리즘을 함수형 방식으로 구현하고 싶다면 fold를 사용하면 된다

```kotlin
// fold 함수
inline fun <R> Iterable<T>.fold(
	initial: R, // 누적자
	operation: (acc: R, T) -> R // 두개의 인자를 받아 새로운 값 리턴
): R 
```

- 위와 같은 함수가 Array를 비롯한 IntArray, DoubleArray 등의 명시적 타입 배열에 정의돼 있다

- fold를 사용해 정수의 합 계산해보자

```kotlin
inline fun <R> Iterable<T>.fold(
	initial: R, // 누적자
	operation: (acc: R, T) -> R // 두개의 인자를 받아 새로운 값 리턴
): R 

fun sum(vararg nums: Int) = nums.fold(0) { acc, n -> acc + n }
// 함수의 마지막 파라미터가 람다식이면 중괄호 부분을 밖으로 뺄 수 있다

fun sum(vararg nums: Int, { acc, n -> acc + n}} // 위랑 똑같다

var numbers = intArrayOf(3, 1, 4, 1, 5, 9)
println(sum(*numbers)) // 23
```

- 이번에는 팩토리얼을 구현해보자

```kotlin
fun factorial(n: Long): BigInteger = 
	when(n) {
		0L, 1L -> BigInteger.ONE
		else -> (2..n).fold(BigInteger.ONE) { 
				acc, i -> acc * BigInteger.valueOf(i) 
			}
	}
	
// when은 switch, case 같은 것
// 0, 1이 들어오면 1을 반환
// 그 외에는 2 ~ n 까지 배열에 대해 fold 를 통해 팩토리얼 계산	
```

### 레시피 4.2 reduce 함수를 사용해 축약하기

- 비어 있지 않은 컬렉션의 값을 축약하고 싶지만 누적자의 초기값을 설정하고 싶지 않은 경우 `reduce` 를 사용하면 된다

```kotlin
inline fun <S, T : S> Iterable<T>.reduce(
	operation: (acc: S, T) -> S
): S
```

- fold랑 달리 누적자가 없다, 그래서 누적자의 초기값으로 컬렉션의 첫 번째 값을 갖는다

- 밑은 코틀린 표준 라이브러리의 reduce 함수 구현이다

```kotlin
public inline fun IntArray.reduce(
	operation: (acc: Int, Int) -> Int): Int {
		if (isEmpty())
			throw UnsupportedOperationException("")
	var accumulator = this[0]
	for (index in i..lastIndex) {
		accumulator = operation(accumulator, this[index])
	}
	return accumulator
}
```

```kotlin
fun sumReduce(vararg nums: Int) = nums.reduce { acc, i -> acc + i }

val numbers = intArrayOf(1, 2, 3)
print(sumReduce(*numbers))
```

- Reduce는 첫 번째 값으로 누적자를 초기화하고 컬렉션의 다른 값에 추가 연산을 필요로 하지 않는 경우에만 reduce를 사용하라

```kotlin
fun sumReduceDoubles(vararg nums: Int) = 
	nums.reduce { acc, i -> acc + 2 * i }

val numbers = intArrayOf(1, 2, 3)
sumReduceDoubles(*numbers) // 7
```

### 레시피 4.3 꼬리 재귀 적용하기

- 재귀 프로세스를 실행하는 데 필요한 메모리를 최소화하고 싶은 경우 **꼬리 재귀**를 사용하자
    - 재귀는 함수 스택에 쌓이게 되고 일정 이상 쌓이게 되면 스택 오버플로를 일으킬 수 있습니다
    - 이것을 꼬리호출 최적화를 통해 해결할 수 있습니다
    - 작성은 재귀 코드로 컴파일은 반복 프로세스로 하는 것이 꼬리호출 최적화

```kotlin
@JvmOverloads
tailrec fun factorial(
	n: Long,
	acc: BigInteger = BigInteger.ONE
): BigInteger = 
	when(n) {
		0L -> BigInteger.ONE
		1L -> acc
		else -> factorial(n - 1, acc * BigInteger.valueOf(n))
}
```

- `@JvmOverloads` : 자바에는 디폴트 인자를 명시할 수 없으므로, 이 어노테이션을 통해 두 번쨰 인자 없이 호출 가능하게 해준다
- `tailrec` : 컴파일러가 반복문으로 재귀를 최적화

- 주의할 점
    - 재귀 부분은 반드시 마지막에 호출되어야 한다
        
        ```kotlin
        tailrec fun factorial(n: Long) = n * factorial(n - 1)
        ```
        
        - 위 코드는 꼬리 재귀로 최적화되지 않는다
        - 보기에는 마지막이지만 사실 마지막으로 호출되는 연산은 `*`
    - 오직 JVM 백엔드에서만 꼬리 재귀가 지원된다
