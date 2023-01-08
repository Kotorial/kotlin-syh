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
