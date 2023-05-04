## replace constructor with builder
생성자에서 __replace constructor with builder__ 를 실행하면, 생성자에 맞는 builder클래스 코드를 생성해주는 기능

```java
public class ReplaceConstructorWithBuilder {

    static class A {
        private final int a;

        public A(int a) {
            this.a = a;
        }
    }
    
    @Test
    void use() {
        new A(10);
    }
}
```

replace constructor with builder > Using existing (지정된 위치에 클래스가 존재하면 그 위치에 Builder 클래스를 생성함)


```java
public class ReplaceConstructorWithBuilder {

    static class A {
        private final int a;

        public A(int a) {
            this.a = a;
        }
    }
    
    @Test
    void use() {
        new ABuilder().a(10).createA();
    }

    public class ABuilder {
        private int a = 1_000;

        public ABuilder a(int a) {
            this.a = a;
            return this;
        }

        public A createA() {
            return new A(a);
        }
    }
}
```

replace constructor with builder > Create new(지정된 패키지에 클래스 파일을 새로 생성함)

```java
public class ReplaceConstructorWithBuilder {

    static class A {
        private final int a;

        public A(int a) {
            this.a = a;
        }
    }
    
    @Test
    void use() {
        new ABuilder().a(10).createA();
    }

    
}
// ABuilder.java
public class ABuilder {
    private int a = 1_000;

    public ABuilder a(int a) {
        this.a = a;
        return this;
    }

    public A createA() {
        return new A(a);
    }
}
```