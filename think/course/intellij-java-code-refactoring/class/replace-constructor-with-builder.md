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
![image](https://user-images.githubusercontent.com/1481137/236134696-be66cf23-37f4-487c-9fe8-e7be53ed95af.png)


replace constructor with builder > Using existing (지정된 위치에 클래스가 존재하면 그 위치에 Builder 클래스를 생성함)
![image](https://user-images.githubusercontent.com/1481137/236135029-c6dca97b-15af-47ec-8e80-e1511a1f46f8.png)



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
![image](https://user-images.githubusercontent.com/1481137/236134912-b337275d-8101-4156-9e4a-6b0e8324d2bd.png)


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
