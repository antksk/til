## 일급 함수(first-class function)
 FP에서는 함수를 마치 일반 값 처럼 사용해서 인수로 전달하거나, 결과로 반환 받거나, 자료 구조에 저장할 수 있다. 이렇게 일반 값처럼 취급할 수 있는 함수를 일급 함수라고 한다.
즉, ```@FunctionalInterface```로 지정된 interface의 단일 메소드가 일급 함수가 된다. 
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
> __부작용과 고차원 함수__
>
> 부작용을 포함하는 함수를 사용하면 부정확한 결과가 발생하거나 레이스 컨디션(race conditions: 경쟁 상태) 때문에 
> 예상치 못한 결과가 발생할 수 있다. 고차원 함수를 적용할 때도 같은 규칙이 적용된다. 
> 고차원 함수나 메소드를 구현할 때 어떤 인수가 전달 될지 알 수 없으므로 인수가 부작용을 포함할 가능성을 염두에 두어야 한다. 함수를 인수로 받아 사용하면서 코드가 정확히 어떤 작업을 수행하고 프로그램의 상태를 어떻게 바꿀지 예측하기 어려워진다. 디버깅도 어려워질 것 이다. 
> 따라서 인수로 전달된 함수가 어떤 부작용을 포함하게 될지 정확하게 문서화하는 것이 좋다.
>
> Java8 in Action

## 커링(curring) 
 Function를 생성하는 함수 팩토리를 가르쳐 커링이라 부른다. 
```java
static DoubleUnaryOperator curriedConverter(double f, double b){
  return (double x) -> x * f + b;
}

DoubleUnaryOperator convertCtoF = curriedConverter(9.0/5, 32); // 섭씨를 화씨로 변환
DoubleUnaryOperator convertKmtoMi = curriedConverter(0.6214, 0); // 킬로미터를 미터로 변환

```

커링은 x와 y라는 두 인수를 받은 함수 f를 편의를 위해 한 개의 인수를 받는 g라는 함수로 대체하여 단순화 하는  기업이다. 이때 g라는 함수 역시 하나의 인수를 받는 함수를 반환한다. 함수 g와 원래 함수 f가 최종적으로 반환하는 값은 같다.
  즉, f(x,y) = (g(x))(y)가 성립한다.


위와 같은 특성을 사용하여, 여섯 개의 인수를 가진 함수를 커링해서 우선 2, 4, 6번째 인수를 받아 5번째 인수를 받는 함수를 반환하고 다시 이 함수는 남은 1, 3번째 인수를 받는 함수를 반환할 수 있다. 
  이와 같이 여러 과정이 끝까지 완료되지 않은 상태를 가리켜 “함수가 부분적으로(partially) 적용되었다”라고 한다.