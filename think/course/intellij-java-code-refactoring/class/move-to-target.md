## Move Instance Method
A class -> B class로 메서드를 옮기는 작업을 진행하는 기능으로 아래의 조건을 만족하면 이동이 가능함
0. 이동하려는 메서드를 ```static```메서드로 먼저 변경하면 자유롭게 이동 가능
1. A class에 B class에 대한 필드가 존재할 경우, A.a메서드를 B로 이동 가능
```java
class A {
    private B b;
    
    public void a(){}
}

class B {
    
}
```
2. A class의 a메서드에서 파라미터로 B class의 인스턴스를 사용하는 경우 
```java
class A {
    public void a(B b){}
}

class B {
    
}
```

만약, A class에 다른 필드들을 a메서드가 사용하고 있는 경우라면, 인스턴스 필드로 이동을 시도 함
```java
class A {
    private int other
    public void a(B b){
        System.out.println(other); // use other field
    }
}

class B {
    
}
```
move instnace ...
```java
class A {
    private int other;
}

class B {
    public void a(A a){
        // use A class의 a.other field사용하려고 시도 하나,
        // other 필드의 접근 레벨로 인해 에러 발생후 있으므로 추가 리펙토링 필요함 
        System.out.println(a.other); 
    }
}
```