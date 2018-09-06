## 00. 책임(Responsibility)
- 특정 액터의 요구사항을 만족시키기 위한 일련의 함수의 집합
- 변경의 원인을 의미하는 지점
- 각 책임에 대한 분류를 의미

## 01. SRP(Single Responsibility Principle) : 단일 책임 원칙
---
* 이 단계의 목적은 모듈들을 분리하고 반드시 하나의 액터만을 위해 존재 하도록 구성
* Use Case를 분해하여, SRP를 준수하는 모듈로 격리(isolate)

> 단일 클래스는 __단 하나의 책임(변경 사유)__ 을 가져야 한다는 원칙

### solution
- Inverted Dependencies : 의존성이 존재하는 Actor들에게 Interface를 제공하여 간접적인 참조를 할수 있도록 수정
- Extract Classes : 각각의 Actor들에게 단일 책임을 갖는 클래스로 분리하여 의존성을 갖도록 구성
- Facade : Actor들이 사용하고 기능을 완벽하게 제거할수 없다면 하위 클래스에 위임하는 facade를 구현하여 적용(레거시 시스템을 지원하기 위해서 사용되는 경우가 많음)
- Interface Segregation : 각 Actor 별로 의존된 기능을 각각의 인터페이스로 분리하고 이렇게 분리된 모든 인터페이스를 단일 객체가 구현하는 방식

## 02. OCP(Open Closed Principle) : 개방 폐쇄 원칙
---
> 확장에 대해서는 __개방__ 되어 있지만, 수정에 대해선 __폐쇄__ 되게 해야 한다는 원칙

사실상 현실 상황에서 모든 객체에 대해 OCP를 적용할 수 없다. 
또한 OCP를 준수하기 위해서 추상화를 너무 많이 사용하면 그만큼 비용이 많이 든다.

위를 해결 하기 위해서는 __Agile Design__ 를 통해, 먼저 빠르게 개발하여 경험하고 
이렇게 얻은 빠른 경험을 통해 OCP를 적용하자. 

## 03. LSP(Liskov Substitusion Principle) : 리스코프 치환 원칙
---
> SubClass는 언제나 SupperClass를 __교체__ 할수 있다는 원칙

즉, 부모클래스(SupperClass) 자리에 자식클래스(SubClass)를 넣어도 계획대로 잘 동작해야 되는 것을 의미함

## 4. ISP(Interface Segregation Principle) : 인터페이스 분리 원칙
---
> Client에서 전달될 Interface method 들 중 사용되지 않는 method는 전달 되지 않아야 한다는 원칙

- 인터페이스 기능 단위를 최소화 하여 client에 전달해야 하며,
먄약 하나의 인터페이스에 여러 기능을 담아 전달 해야 되는 경우,
여러개의 기능별 분리된 인터페이스로 나누고 이를 상속하여 하나의 인터페이스를 전달하여 구성
- Inteface는 구현체보다는 클라이언트와 논리적으로 결합되므로 클라이언트가 호출하는 메소드만 Interface에 정의되었다는 것을 확신할수 있음

## 5. DIP(Dependency Inversion Principle) : 의존성 역전 원칙
---
> 상위모듈(기능모듈)은 하위모듈(구현모듈)에 의존해서는 안된다는 원칙(중간에 추상 모듈을 끼워 넣어 분리)

첫째, 상위 모듈은 하위 모듈에 의존해서는 안된다. 상위 모듈과 하위 모듈 모두 추상화에 의존해야 한다.
둘째, 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.


[gitbook-SOLID-설명잘됨]: https://trazy.gitbooks.io/oop/content/oop-srp.html
[DIP-넘어-IoC]: http://javacan.tistory.com/110
[객체 지향 프로그래밍/원칙]: https://namu.wiki/w/%EA%B0%9D%EC%B2%B4%20%EC%A7%80%ED%96%A5%20%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/%EC%9B%90%EC%B9%99
