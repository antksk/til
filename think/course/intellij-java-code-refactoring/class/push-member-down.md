## push member down
super클래스에 있는 멤버를 아래(sub) 클래스로 내릴때 사용하는 기능

```java
public class PushMemberDown {
    
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

push member down
![image](https://user-images.githubusercontent.com/1481137/236135315-687e3249-c1d4-42fd-baf8-2feddee72fcc.png)


```java
public class PushMemberDown {

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

push member down > keep abstract(멤버를 abstract 키워드를 붙이고, 아래 멤버에 @Override 처리함)
```java
public class PushMemberDown {

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
