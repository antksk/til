## pull member up
sub 클래스에 존재하는 멤버를 super 클래스로 올려 주는 기능

```java
public class PullMemberUp {

    static class A {
    }

    static class B extends A{

        public void a(){

        }
    }

    @Test
    void use() {
        B b = new B();
        b.a();
    }
}
```

pull member up

```java
public class PullMemberUp {

    static class A {
        public void a(){

        }
    }

    static class B extends A{

    }

    @Test
    void use() {
        B b = new B();
        b.a();
    }
}
```

pull member up > Make abstract(super를 추상화 해줌)
```java
public class PullMemberUp {

    static abstract class A {
        public abstract void a();
    }

    static class B extends A{

        @Override
        public void a(){

        }
    }

    @Test
    void use() {
        B b = new B();
        b.a();
    }
}
```