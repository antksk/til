## Extract Interface
클래스 이름에서 __Extract Interface__를 하게 되면,
지정된 클래스에서 사용하던 public 메서드나 상수 들에 대해서 생성된 Interface로 추출 하는 기능

### Action Window > Extract Interface

![image](https://user-images.githubusercontent.com/1481137/236083470-be3b0e79-2726-49f5-a6b5-98723be10f80.png)

![image](https://user-images.githubusercontent.com/1481137/236083610-9824cf14-f1e5-4c3b-98d9-a4a5d5ec22bf.png)

```java
class AClass {
    
    static double CONSTANT = 3.14;

    public void publicMethod() {//some code here
    }

    public void secretMethod() {//some code here}
    }
}

// use
var aClass = new AClass();
aClass.publicMethod();
System.out.println(AClass.CONSTANT);
```

after refactor
```java
interface AInterface {
    double CONSTANT = 3.14;
    void publicMethod();
}

class AClass implements AInterface {
    
    @Override
    public void publicMethod() {//some code here
    }

    public void secretMethod() {//some code here}
    }
}

// use
var aInteface = new AClass();
aInteface.publicMethod();
System.out.println(AInterface.CONSTANT);
```