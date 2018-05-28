# List<? super T> vs List<? extends T>
참조 : https://www.youtube.com/watch?v=PQ58n0hk7DI > 34:39

참조 코드 
```java
public class Collections {
    public static <T> void copy(List<? super T> dest, List<? extends T> src) {
        int srcSize = src.size();
        if (srcSize > dest.size())
            throw new IndexOutOfBoundsException("Source does not fit in dest");

        if (srcSize < COPY_THRESHOLD ||
            (src instanceof RandomAccess && dest instanceof RandomAccess)) {
            for (int i=0; i<srcSize; i++)
                dest.set(i, src.get(i));
        } else {
            ListIterator<? super T> di=dest.listIterator();
            ListIterator<? extends T> si=src.listIterator();
            for (int i=0; i<srcSize; i++) {
                di.next();
                di.set(si.next());
            }
        }
    }
}
```

외부에서 사용되는 경우 ```List<? super T>```을 사용하고,
내부에서 소비되는 경우 ```List<? extend T>```를 사용

#Intersection Type
추론된 파라미터 타입을 캐스팅 하는 것
```java
public class IntersectionType{
    public static void main(String[] args){
        hello((Function & Serializable)s->s);
    }
    private static void hello(Fuctnion o){
        
    }
    // or
    private static <T extends  Function & Serializable & Cloneable> void hello(T o){
        
    }
}
```

# mixing
```java
public class IntersectionType{
    interface Hello{
        default void hello(){
            System.out.println("Hello !");
        }
    }
    interface Hi{
        default void hi(){
            System.out.println("Hi !");
        }
    }
    public static void main(String[] args){
        hello((Function & Hello & Hi)s->s);
    }
   
    private static <T extends  Function & Hello & Hi> void hello(T o){
        o.hello();
        o.hi();
        o.apply("aaa");
    }
}
```

위의 코드는 run을 호출하는 쪽과 정의된 쪽에 ```<T extends  Function & Hello & Hi>```에 대한 정보를 
알고 있어야 한다는 단점이 있음

아래와 같이 콜백을 사용하여 해결 가능
```java
public class IntersectionType{
    interface Hello{
        default void hello(){
            System.out.println("Hello !");
        }
    }
    interface Hi{
        default void hi(){
            System.out.println("Hi !");
        }
    }
    public static void main(String[] args){
        hello((Function & Hello & Hi)s->s, c -> {
            c.hello();
            c.hi();
            c.apply("aaa");
        });
    }
    
    private static <T extends  Function> void hello(T f, Consumer<T> consumer){
        consumer.accept(f);
    }
}
```

induction 처리가 완료 되고 나서 같은 조합의  함수 타입을 가지고 있으면, 아래와 같은 코드가 동작한다.
```java
public class IntersectionType{
    interface Hello extends Function{
        default void hello(){
            System.out.println("Hello !");
        }
    }
    interface Hi extends Function{
        default void hi(){
            System.out.println("Hi !");
        }
    }
    public static void main(String[] args){
        run((Function & Hello & Hi)s->s, c -> {
            c.hello();
            c.hi();
            c.apply("aaa");
        });
    }
    
    // Hello, Hi 모두 
    private static <T extends  Function> void run(T f, Consumer<T> consumer){
        consumer.accept(f);
    }
}
```

default 함수 조합은 비즈니스로직에 크게 도움이 되지 않음, delegate를 사용 
```java
public class IntersectionType{
    interface DelegateTo<T>{
        T delegate();
    }
    interface Hello extends DelegateTo<String>{
        default void hello(){
            System.out.println("Hello !" + delegate());
        }
    }
    interface UpperCase extends DelegateTo<String>{
        default void upperCase(){
            System.out.println(delegate().toUpperCase());
        }
    }
    public static void main(String[] args){
        run((DelegateTo<String> & Hello & Hi)() -> "Ant", c -> {
            c.hello();
            c.upperCase();
        });
    }
    
    // Hello, Hi 모두 
    private static <T extends  DelegateTo<S>, S> void run(T f, Consumer<T> consumer){
        consumer.accept(f);
    }
}
```

### delegate 활용(Forwarding)
```java
public class IntersectionType{
    interface DelegateTo<T>{
        T delegate();
    }
   
    interface Pair<T>{
        T getFirst();
        T getSecond();
        void setFirst(T first);
        void setSecond(T second);
    }
    
    @Value
    class Name implements Pair<String>{
        private String first;
        private String second;
    }
    
    // Pair 기능을 default 메서드로 일종에 오버로딩되게 처리함
    interface ForwardingPair<T> extends DelegateTo<Pair<T>>, Pair<T>{
        default T getFirst(){return delegate().getFirst();}
        default T getSecond(){return delegate().getSecond();}
        default void setFirst(T first){delegate().setFirst(frist);}
        default void setSecond(T second){delegate().setFirst(second);}
    }
   
    public static void main(String[] args){ 
        final Pair<String> name = new Name("ant", "Kim");
        run((ForwardingPair<String>)() -> name, c -> {
            c.getFirst();
            
        });
    }
    
    // Hello, Hi 모두 
    private static <T extends  DelegateTo<S>, S> void run(T f, Consumer<T> consumer){
        consumer.accept(f);
    }
}
```