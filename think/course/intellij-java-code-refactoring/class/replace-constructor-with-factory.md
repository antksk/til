## replace constructor with factory
클래스에 있는 생성자를 private 접근자로 변경 후에 public static 팩터리 매서드를 생성해주는 기능
```java
public class ReplaceConstructorWithFactory {
    static class A {
        private final int a;
        // 생성자 이름 부분 선택후 "replace constructor with factory"
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

replace constructor with factory

```java
public class ReplaceConstructorWithFactory {
    static class A {
        private final int a;

        private A(int a) {
            this.a = a;
        }

        public static A create(int a) {
            return new A(a);
        }
    }

    @Test
    void use() {
        A.create(10);
    }
}
```