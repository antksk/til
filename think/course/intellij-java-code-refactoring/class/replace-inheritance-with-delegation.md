## replace inheritance with delegation
상속으로 구현되어 있는 sub 클래스에서 __replace inheritance with delegation__ 를 사용하면, 
super 클래스와의 extends 관계를 끝고, sub 클래스에서 has-a관계로 변경함

```java
class A {
    public void a(){
        
    }
}

class B extends A {

}

@Test
void use() {
    B b = new B();
    b.a();
}
```

replace inheritance with delegation

![image](https://user-images.githubusercontent.com/1481137/236135840-75fd19df-dde7-4a8b-9dd5-f0bc6275d50e.png)



```java
class A {
    public void a(){
        
    }
}

class B {

    private final A a = new A();

    public void a() {
        a.a();
    }
}

@Test
void use() {
    B b = new B();
    b.a();
}
```
