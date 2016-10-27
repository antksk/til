# JAVA8 FP를 공부내용 정리
## Optional 객체

* 기존에 java를 사용하면서, 객체에 대한 null 체크를 수동하거나,
아니면 empty객체를 만들어 대응하였으나, 
JAVA8부터는 ```Optional<?>``` 객체를 활용하여, 
null 객체에 대한 적절하게 대응할 수 있도록 개선됨  

#### void ifPresent(Consumer<? super T> consumer)
* target 객체가 null이 아니면 consumer fp가 호출
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
```java
```