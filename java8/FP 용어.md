## 불변객체(immutable object)
불변객체는 한번 생성된 후 그 상태가 변경되지 않는 객체를 의미 하며, 이런 상태로 인해 멀티 스레드 환경에서 안전하다.
또한 그 상태를 알수 없거나 예외 때문에 잘못된 상태를 가질 가능성이 적다. 왜냐하면 모든 초기화가 생성시에 일어 나기 때문에 자바에서는 
원자적(atomic)이며 그로 인해 어떤 예외도 객체가 생성되기 전에 발생하기 때문이다.
이러한 성격으로 인해, __불변객체는 ```Set```, ```Map```등의 자료구조에서 Key로 사용되기 매우 좋다.__

### 불변객체 조건
- 모든 필드는 ```final```로 선언되어야 한다.
- 클래스를  ```final```로 선언해서 오버라이드를 방지해야 한다.
- 인수가 없는 생성자는 가급적이면 배제한다. 


## 일급 함수(first-class function)
 FP에서는 함수를 마치 일반 값 처럼 사용해서 인수로 전달하거나, 결과로 반환 받거나, 자료 구조에 저장할 수 있다. 
이렇게 일반 값처럼 취급할 수 있는 함수를 일급 함수라고 한다.
Java8의 경우, ```@FunctionalInterface```로 지정된 interface의 단일 메소드가 일급 함수가 된다. 
```java
 // FP 객체를 변수 처럼 취급
 Function<String, Integer> starToInt = Integer::parseInt;
 
 // 혹은
 
 Map<String, Function<String,Integer>> m = new ImmutableMap.Builder<String,Function<String,Integer>>()
   .put( "test-a", (i)->"test" + i )
   .put( "test-b", (i)->"test test " + i) )
   .put( "test-c", (i)->"aaaa " + i) )
 .build(); 
```


## 고차원 함수(higher-order function)
하나 이상의 함수를 인수로 받거나, 함수를 결과로 반환하는 함수를 고차원 함수라고 한다.

대표적인 예로, map 함수가 있음

```java
// 01. map고차 함수를 이용해 데이터 가공 예제
Arrays.asList("a", "b", "c").stream()
    .map(String::toUpperCase)
    .forEach(System.out::println);

// 02. 위키 참조 예제
Function<Function<Integer, Integer>, Function<Integer, Integer>> twice = f -> f.andThen(f);
twice.apply(x -> x + 3).apply(7); // 13
```

> __부작용과 고차원 함수__
>
> 부작용을 포함하는 함수를 사용하면 부정확한 결과가 발생하거나 레이스 컨디션(race conditions: 경쟁 상태) 때문에 
> 예상치 못한 결과가 발생할 수 있다. 고차원 함수를 적용할 때도 같은 규칙이 적용된다. 
> 고차원 함수나 메소드를 구현할 때 어떤 인수가 전달 될지 알 수 없으므로 인수가 부작용을 포함할 가능성을 염두에 두어야 한다. 함수를 인수로 받아 사용하면서 코드가 정확히 어떤 작업을 수행하고 프로그램의 상태를 어떻게 바꿀지 예측하기 어려워진다. 디버깅도 어려워질 것 이다. 
> 따라서 인수로 전달된 함수가 어떤 부작용을 포함하게 될지 정확하게 문서화하는 것이 좋다.
>
> Java8 in Action


## 커링(curring) 
- Function를 생성하는 함수 팩토리를 가르쳐 커링이라 부른다. (일급 함수(first-class function)를 생성하는 함수(팩토리)를 가르켜 커링이라 불음)
- 커링을 자주 하게 되면, 함수의 파라미터 갯수가 주는 효가를 가짐

```java
static DoubleUnaryOperator curriedConverter(double f, double b){
  return (double x) -> x * f + b;
}

DoubleUnaryOperator convertCtoF = curriedConverter(9.0/5, 32); // 섭씨를 화씨로 변환
DoubleUnaryOperator convertKmtoMi = curriedConverter(0.6214, 0); // 킬로미터를 미터로 변환
```

> 커링은 x와 y라는 두 인수를 받은 함수 f를 편의를 위해 한 개의 인수를 받는 g라는 함수로 대체하여 단순화 하는  기법이다.
> 이때 g라는 함수 역시 하나의 인수를 받는 함수(커링)를 반환한다. 함수 g와 원래 함수 f가 최종적으로 반환하는 값은 같다.
> 즉, f(x,y) = (g(x))(y)가 성립한다.

위와 같은 특성을 사용하여, 여섯 개의 인수를 가진 함수를 커링해서 우선 2, 4, 6번째 인수를 받아 5번째 인수를 받는 함수를 반환하고 다시 이 함수는 남은 1, 3번째 인수를 받는 함수를 반환할 수 있다. 
  이와 같이 여러 과정이 끝까지 완료되지 않은 상태를 가리켜 “함수가 부분적으로(partially) 적용되었다”라고 한다.

### 커링(currying) vs 부분 적용(partial application)
- __커링__ :  다인수 함수를 일 인수 함수들의 체인으로 바꾸 방법
- __부부 적용__ : 다인수 함수에 부분적으로 생략 가능한 인수의 값이 미리 정해 더 작은 수의 인수를 받는 하나의 함수로 변환하는 방법

개발 관점에서 커링은 함수 팩토리, 부분 적용은 메소드 오버로딩 관점으로 해석될 수 있다.

#### 차이점
커링은 체인형태로 다음 함수를 리턴하는 반면, 부분 적용은 인수가 더 작은 함수를 만들어 줌
예를 들어, 인수가 3이상일 경우 ```process( x, y, z )```의 완전한 커링된 버전은 ```process(x)(y)(z)```와 같은 체인 구조이다. 
여기에서 ```process(x)```와 ```process(x)(y)```는 인수가 하나인 함수이다. 
첫 인수만 커링을 하면 ```process(x)```의 리턴 값은 인수가 하나인 또하나의 함수를 리턴한다. 
반면에 부분 적용은 인수 숫자가 적은 함수가 남는다. 예를 들어, ```process(x, y, z)```의 인수 하나를 
부분적용하면 인수 두 개짜리 ```process(y,z)```가 된다.


## 평탄화(flattening-플래트닝)
중첩목록은 함수형 프로그래밍 언어에 자주 등장하기 때문에 보통 중첩을 펼치는 연산을 라이브러리에서 지원한다.

아래와 같이 Optional 클래스의 flatMap을 예로 들수 있다.
절대 null 아닌 값을 검증하여, 문자열을 반환하는 메소드가 있다면,
```java
public String findSimilar(@NotNull String s)
```

findSimilar를 호출하기 전에 검증하는 것은 귀찮은 일이다.

```java
String similarOrNull = x != null? findSimilar(x) : null;
```
findSimilar 를 다시 정의 해서 Optional를 호출하게 해도

```java
public Optional<String> tryFindSimilar(String s)
```

호출 받는 쪽에선 이중 ```Optional<Optional<String>>```를 만들어야 할지도 모른다.

```java
Optional<Optional<String>> bad = opt.map(this::tryFindSimilar);
```

이때, flatMap를 사용하면, 리턴되는 Optional를 검증해 주기 때문에, 깔끔한, Optional를 얻을 수 있다.

Optional 클래스의 flatMap 구현체는 다음과 같다.
```java
public <U> Optional<U> flatMap(Function<T, Optional<U>> mapper) {
    if (!isPresent())
        return empty();
    else {
        return mapper.apply(value);
    }
}
```


## 모나드(monad)
참고 : [케빈채널 e03 – 함수형 프로그래머를 찾아서 – 한주영 4부][케빈채널 e03 – 함수형 프로그래머를 찾아서 – 한주영 4부]

[케빈채널 e03 – 함수형 프로그래머를 찾아서 – 한주영 4부]: https://iamprogrammer.io/2017/02/23/%EC%BC%80%EB%B9%88%EC%B1%84%EB%84%90-e03-%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%A8%B8%EB%A5%BC-%EC%B0%BE%EC%95%84%EC%84%9C-%ED%95%9C%EC%A3%BC%EC%98%81-2/

서로 다른 일을 하는 두개의 함수를 합성 할때 사용하는 행위

예를 들어, 
int => Optional<String> => Optional<Student> =>  Student를 가져 오는 형식을 취한다고 했을때,

Optional<String> => Optional<Student> 에서 Optional이 중첩이 발생하기 때문에,
위 문제를 해결 하기 위해서 flatMap 하여 Student를 뽑아 낼때 사용하는 방식(여기서 모나드와 플래트닝이 발생함)

* free 모나드: list 형태로 모나드를 저장해 놓고, 결과를 계산할때 하나씩 적용해서 결과를 만들어 가는 형태
* state 모나드: for 문장을 구성할때, i와 같이 각 동작에 대한 상태를 저장해야 하는 상태 정보 


## 모노이드
더하기가 가능한 타입( int, String 등 )
free 모노이드 : list로 저장해서 연결하는(더하기)를 구현한 형태


## 메모이제이션(memoization)
- 메모이제이션은 프로그래밍 언어에 내장되어 반복되는 함수의 리턴 값을 자동으로 캐싱하는 기능을 의미함
- 특정 언어 자체에서 제공하는 캐시 기능이기 때문에 최적화된 성능으로 코드를 거의 바꾸지 않고 캐시 혜택을 누릴 수 있음
- 부수효과가 없어야 함, 그렇지 않으면 캐시된 값이 리턴되어도 그 값을 믿을 수 없게 됨
- 외부 정보에 절대로 의존하지 말아야 함

Java8에는 메모이제이션을 지원하지 않지만, ```Funcation<T,U>``` 인터페이스와 컬렉션의 ```computeIfAbsent```메소드를 쓰면 비교적 쉽게 구현이 가능함.
```java
public class Memoizer<T, U> {

  private final Map<T, U> cache = new ConcurrentHashMap<>();

  private Memoizer() {}

  private Function<T, U> doMemoize(final Function<T, U> function) {
    return input -> cache.computeIfAbsent(input, function::apply);
  }

  public static <T, U> Function<T, U> memoize(final Function<T, U> function) {
    return new Memoizer<T, U>().doMemoize(function);
  }
}

Function<Integer, Integer> f = x -> x * 2;
Function<Integer, Integer> g = Memoizer.memoize(f); // f에 의해서 계산된 결과를 cache에 저장하고 g 함수를 호출할때 cache에서 꺼내옴
```

## 게으름(lazy)
표현의 평가를 가능한 최대로 늦추는 기법으로 대표적 예로 게으른 컬렉션이 있음, 
이를 사용하면 그 요소들을 한꺼번에 미리 연산하는 것이 아니라 필요에 따라 하나씩 기능 별로 전달로 전달하여 계산을 늦출 수 있음

- 시간이 많이 걸리는 연산을 필요한 때까지 미룰 수 있음
- 무한 컬렉션을 만들 수 있음
- 컬렉션 전부를 유지하지 않고 순차적으로 다음 값을 유도할 수 있으므로 메모리 저장 크기가 줄어 든다.
- 맵이나 필터 같은 함수형 개념을 게으르게 사용하면 효율이 높은 코드를 만들 수 있음

Java8 Stream으로 lazy한 무한 자연수 수열 만들기
```java
IntStream.iterate(1, i -> i + 1);
```