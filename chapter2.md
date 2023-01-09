### 레시피 2.6 숫자를 거듭제곱하기
* 코틀린에서는 미리 정의된 거듭제곱 연산자가 존재하지 않습니다
* 자바에서는 Math.pow(double, double) 이 있고, int나 long을 double로 자동 형변환을 시켜준다
* 하지만 코틀린에는 기본 타입이 없고 Integer나 Double을 자동 승격하지 않는다
* 그래서 거듭제곱이 필요하면 Float나 Double의 pow를 사용해야 한다

```
    2.toDouble().pow(8).toInt();
```

* Int나 Long에 시그니처 확장 함수를 정의함으로써 처리 과정을 자동화할 수 있다

```
    fun Int.pow(x: Int) = toDouble().pow(x).toInt()
```

* 혹은 Infix 연산자를 정의할 수 있다
    * Infix 연산자란?
        * 두 값 사이에 특정한 표현식을 넣는 연산자
        * 1 to 'a'
        * a as b 
        * 등등

```
    infix fun Int.`**` = toDouble().pow(x).toInt()
    fun Int.pow(x: Int) = `**`(x)
    
    print(2 `**` 2)
    print(2.pow(2))
```

### 레시피 2.7 비트 시프트 연산자 사용하기

- 코틀린에는 비트 시프트를 위한 shr, shl, ushr  같은 비트 중위 연산자가 있다
    - 코틀린은 위의 시프트 연산자를 Int와 Long에 정의해 놓았다
    - `shl` : 왼쪽으로 쉬프트 연산
    - `shr` : 오른쪽으로 쉬프트 연산

```kotlin
fun main(args: Array<String>) {
    println(1 shl 8) // 256
    println(256 shr 8) // 1
}
```

- 부호에 의존하지 않고 값을 시프트하는 경우 `ushr` 함수를 사용하면 된다

```kotlin
fun main(args: Array<String>) {
    println(-5 shl 1) // -10
    println(-5 shr 1)  // -3
    println(-5 ushr 1) // 2147483645
}
```

- `shr` 의 경우 왼쪽으로부터 1을 채운다
    - `110110 -> 111011`
- `ushr` 의 경우 왼쪽을 0으로 채운다
    - 0으로 채우기 때문에 음수 부호를 보존하지 않는다
- 정수 2개의 중간 값을 찾는 경우 `ushr` 를 사용할 수 있다
    - `val mid = (high + low) ushr 1`
    - 오버플로를 방지할 수 있다
