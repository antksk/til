## 1. SRP(Single Responsibility Principle) : 단일 책임 원칙
* 이 단계의 목적은 모듈들을 분리하고 반드시 하나의 액터만을 위해 존재 하도록 구성
* Use Case를 분해하여, SRP를 준수하는 모듈로 격리(isolate)

> 모든 클래스는 __단 하나의 책임__을 가져야 한다는 원칙

 

## 2. OCP(Open Closed Principle) : 개방 폐쇄 원칙
> 확장에 대해서는 __개방__되어 있지만, 수정에 대해선 __폐쇄__되게 해야 한다는 원칙

## 3. LSP(Liskov Substitusion Principle) : 리스코프 치환 원칙
> SubClass는 언제나 SupperClass를 __교체__ 할수 있다는 원칙

즉, 부모클래스(SupperClass) 자리에 자식클래스(SubClass)를 넣어도 계획대로 잘 동작해야 되는 것을 의미함

## 4. ISP(Interface Segregation Principle) : 인터페이스 분리 원칙
> Client에서 전달될 Interface method 들 중 사용되지 않는 method는 전달 되지 않아야 한다는 원칙

인터페이스 기능 단위를 최소화 하여 client에 전달해야 하며,
먄약 하나의 인터페이스에 여러 기능을 담아 전달 해야 되는 경우, 
여러개의 기능별 분리된 인터페이스로 나누고 이를 상속하여 하나의 인터페이스를 전달하여 구성

## 5. DIP(Dependency Inversion Principle) : 의존성 역전 원칙
> 상위모듈(기능모듈)은 하위모듈(구현모듈)에 의존해서는 안된다는 원칙(중간에 추상 모듈을 끼워 넣어 분리)

첫째, 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.

둘째, 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.


[gitbook-SOLID-설명잘됨]: https://trazy.gitbooks.io/oop/content/oop-srp.html
[DIP-넘어-IoC]: http://javacan.tistory.com/110
[객체 지향 프로그래밍/원칙]: https://namu.wiki/w/%EA%B0%9D%EC%B2%B4%20%EC%A7%80%ED%96%A5%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/%EC%9B%90%EC%B9%99