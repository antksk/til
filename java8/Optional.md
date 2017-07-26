# JAVA8 FP를 공부내용 정리
## Optional 객체

 기존에 java를 사용하면서, 객체에 대한 null 체크를 수동하거나,
아니면 empty객체를 만들어 대응하였으나, 
JAVA8부터는 ```Optional<?>``` 객체를 활용하여, 
null 객체에 대한 적절하게 대응할 수 있도록 개선됨  

  하지만 Java 자체가 은탄환은 아니기 때문에, ```Optional<Integer>```, ```Optional<String>```등은 모두 같은 Optional 타입이다. 제네릭은 생성된 타입으로 구분되지 않기 때문에, ```void a(Optional<Integer> o)```, ```viod a(Optional<String> o)```는 오버로딩되지 않는다.

그렇다고,  ```class OptionalInteger extends Optional<Integer>``` 상속를 사용 할수 있는 것도 아니다. Optional 클래스는 final 이기 때문이다.

하지만 단점 보다는 장점이 더 많음 !!!!! 아래의 예제 참조 !!!!!

#### void ifPresent(Consumer<? super T> consumer)
* target 객체가 null이 아니면 consumer fp가 호출되어 검증한 target 객체 소비
```java
    String  a = null;
    // java8 이전 ( null 체크 혹은, 임시 empty 객체를 활용하여 검증 )
    if( null != a ){
    ...
    }
    
    // or 
    
    String b = StringUtils.empty();  
    if( "".equal(b) ){
    ...
    }
    
    /**
    * a 객체가 null이면 NullPointerException 발생 시킴 
    */
    Optional<String> o = Optional.of(a);
    /**
    *
    * value == null ? empty() : of(value)
    *  
    * 위와 같이 null를 먼저 체크 하기 때문에,
    *  
    * null인 경우 Optional.empty메소드 호출하여 Optional 객체 반환 (싱글톤으로 미리 정의된 EMPTY 객체 반환) 
    */
    Optional<String> o = Optional.ofNullable(a); 
    
    // Optional target으로 지정된 객체가 null이 아니면 true 리턴
    
    o.ifPresent(t->{ // <-- Consumer<? super T> consumer 파라미터 타입
    // t가 절대로 null이 아니므로, t를 이용하여 이 FP안에서 t 내용 소비
});
```

#### Optional<T> filter(Predicate<? super T> predicate)
* 지정된 predicate를 평가하여, 조건에 맞으면, 다음 진행
```java
/**
    public Optional<T> filter(Predicate<? super T> predicate) {
        Objects.requireNonNull(predicate); // 그럴 일은 거의 없겠지만, predicate가 null이면 NullPointerException 발생
        if (!isPresent()) // 자신이 평가하는 객체가 null이면 bypass
            return this;
        else
            return predicate.test(value) ? this : empty();
    }
**/

// 이렇게 답답 했던 조건들이...
if (x != null && x.contains("ab")) {
    print(x);
}

// Optional로 평가 하게 되면, 이렇게 깔끔하게 바뀜
Optional.ofNullable(x)
	.filter(t -> t.contains("ab")) // --> 여기 부분도 메소드 이름으로 대체 가능 > 예를 들어, this::abContains
	.ifPresent(this::print);
```

#### public<U> Optional<U> map(Function<? super T, ? extends U> mapper)
* 지정된 객체에 대한 변경 후 다시 Optional 객체로 변환함 
```java
/**
    public<U> Optional<U> map(Function<? super T, ? extends U> mapper) {
        Objects.requireNonNull(mapper);
        if (!isPresent())
            return empty();
        else {
            return Optional.ofNullable(mapper.apply(value));
        }
    }
**/

if (x != null) {
    String t = x.trim();
    if (t.length() > 1) {
        print(t);
    }
}

Optional.ofNullable(x)
    .map(String::trim)
        .filter(t -> t.length() > 1)
        .ifPresent(this::print);
        
```

#### public T orElse(T other), 
#### public T orElseGet(Supplier<? extends T> other),
#### public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) throws X
```java
int len = (x != null)? x.length() : -1;

// 객체에 값이 없으면, 기본 값으로 orElse에 있는값 상수 리턴
Optional.ofNullable(x)
    .map(String::length)
    .orElse(-1);

// 혹은,

// 지정된 메소드의 값 리턴
Optional.ofNullable(x)
    .map(String::length)
    .orElseGet(this::slowDefault);
    
// 아니면, 예외 처리
Optional.ofNullable(x)
    .map(String::length)
    .orElseGet(EmptyStringException::new);
```
