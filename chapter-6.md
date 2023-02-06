## recipe 지연 시퀸스 사용하기
* 코틀린은 기본 컬렉션에 시퀸스 확장 함수를 추가해 두었기 때문에 List에는 map과 filter 같은 함수가 있다

```
(100 util 200).map {it * 2}
  .filter{ it % 3 == 0 }
  .first()
```

* 위의 연산은 필요없는 연산을 많이 한다

```
(100 util 200).map {it * 2}
  .first{ it % 3 == 0 }
```

* 위 처럼 특정 조건이 다다를 때까지 오직 필요한 데이터만을 처리하는 방식을 쇼트 서킷이라 부른다
* 코틀린에서는 위 처럼 시퀀스라는 것이 있다
* 기존에는 전체 collection에 대해 각 단계의 수행을 완료하고 넘어간다면, 스퀀스는 하나의 원소에 대해 모든 단계를 수행한다

## recipe 6.2 시퀀스 생성하기
* 시퀀스 생성 방법
  * 이미 원소가 있는 경우 sequenceOf
  * Iterable이 있다면 asSequence
  * 그 외에는 시퀀스 생성기를 사용하면 된다
    * `generateSequence()`

```
val numSequence1 = sequenceOf(3, 1, 4, 2)
val numSequence2 = listOf(3, 1, 4, 1, 5, 9).asSequence()
```

* 이 두 구문은 주어진 리스트로부터 `Sequence<Int>`를 생성한다

```
fun nextPrime(num: Int) = 
  generateSequence(num + 1) { it + 1 }
    .first(Int::isPrime) // 인수로 받은 람다가 true를 반환하면 시퀀스를 종료
```

* 주어진 수에서 1씩 증가시키며 만나는 첫번째 소수를 반환하는 시퀀스


## recipe 6.3 무한 시퀀스 다루기
* 무한대의 원소를 갖는 시퀀스의 일부분이 필요하다
* 널을 리턴하는 시퀀스 생성기를 사용하거나 시퀀스 확장 함수 중에서 takeWhile 같은 함수를 사용하자
* 시퀀스에는 중간 연산(map, filter)과 최종 연산(first, toList)이 있다
  * 시퀀스는 최종 연산이 수행될 때까지 어떤 데이터도 시퀀스의 파이프라인을 통과하지 않는다

```
fun firstNPrimes(count: Int) =
  generateSequence(2, ::nextPrime) // 2부터 시작하는 소수의 무한 시퀀스
    .take(count) // 요청한 수만큼만 원소를 가져오는 중간 연산
    .toList() // 최종 연산
```

* 위의 코드는 정해진 N 개만큼 소수를 구하는 코드이다
* 최초 지정한 다수의 소수를 찾기 위해 얼마나 많은 숫자를 검사해야 하는지 알 수 없다

```
fun primesLessThan(max: Int): List<Int> = 
  generateSqeuence(2) { n -> if (n < max) nextPrime(n) else null }
    .toList()
    .dropLast(1)
```

* 위 nextPrime 함수는 현재 값보다 큰 다음 소수를 찾는 함수이다
* 소수가 지정한 max 값보다 커진다면 null을 반환하고, 해당 null은 시퀀스를 종료한다

```
fun primesLessThan(max: Int): List<Int> = 
  generateSequence(2, ::nextPrime)
    .takeWhile { it < max }
    .toList()
```

* `takeWhile`은 시퀀스에서 제공하는 술어가 true를 리턴하는 동안 시퀀스에서 값을 추출한다



