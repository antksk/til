# DDD(Domain Driven Design)
도메인에 집중하고, ___도메인의 개념을 커뮤니케이션과 코드로 부드럽게 연결 시키려는 과정___이 DDD의 핵심!!!


* 참고 : 
  - 조영호 님의 애플리케이션 [애플리케이션 아키텍쳐와 객체지향][애플리케이션 아키텍쳐와 객체지향] 내용
  - [도메인 주도 설계의 본질][도메인 주도 설계의 본질] 
[애플리케이션 아키텍쳐와 객체지향]: http://www.slideshare.net/baejjae93?utm_campaign=profiletracking&utm_medium=sssite&utm_source=ssslideview
[도메인 주도 설계의 본질]: http://www.slideshare.net/baejjae93/ss-27536729


* 용어 :
  - __엔티티(Entity)__ : 식별성을 지닌 영속 객체(JPA의 @Entity를 가진 객체) 
  - __값 객체(Value Object)__ : 식별성이 아닌 속성을 이용해 정의 되는 객체 __불변객체__를 사용하여 자주 정의됨(JPA에서 @Embedded로 정의된 객체)
  Entity의 속성 뿐만 아니라 다른 Value Object의 속성으로도 사용됨
  - __애그리거트(Aggregate)__ : 연관된 Entity와 Value Object의 묶음, 트랜잭션 의 분산 단위, 캡슐화를 통한 복잡성 관리,
  예를 들어, Order Entity와 OrderItem ValueObject, Orderer(주문자) ValueObject를 "주문 애그리거트"로 묶을 수 있음
  - __리포지터리(Repository)__ : 도메인 모델의 영속성 처리를 담당, 예를 들어, RDBMS테이블에서 Entity객체를 로딩 하거나 저장하는 기능을 제공함
  - __Factory__ : 생성하기 복잡한 Aggregate내의 여러 객체들을 동시에 생성하는 역활을 담당하는 객체로 Aggregate의 일관성 유지
  - __Bounded Context__ : 특정한 Domain Model이 적용되는 제한된 영역, 경계내에선 동일한 모델을 일관되게 적용, 경계 밖의 일관성은 고려 대상이 아님
  	- bounded context는 독립적으로 분리되어도 상관없는 업무 범위를 의미
  	- 예) 쇼핑몰의 판매 컨텍스트(sales context), 판매 지원 컨텍스트(support context), 결제 컨텍스트(payments context)등
  	   또는, 포털 사이트에서 검색 기능 컨텍스트, 블로그, 카페, 게시판 등의 컨텍스트
  	   Bounded Context 관계를 표현할때 중요한 요소는 서로 관계를 맺고 있는 컨텍스트간의 명확한 통신 규약을 표현하는 것임(컨텍스트 맵)
  - __Ubiquitous language__ : 보편언어(Ubiquitous Language)는 도메인 전문가 혹은 사용자와 개발자 사이의 의사 소통 어려움을 보편적인 언어(용어정리를 통한 일반화된 단어사용)를 통해 소통의 어려움을 해결 하는 것을 목표로 하는 행위를 의미함
    - 보편언어들로 정의 할때, 고려되어야 하는 사항은 Bounded Context(제한범위)범위 내에서 정의 되어야 함
  - __Service__ : Domain Object에 위치시키기 어려운 오퍼레이션을 가지는 객체, 여러 Domain Object를 다루는 연산, Service의 오퍼레이션은 일반적으로 상태를 가지지 않음(stateless)

  
## Transaction Script
* SI에서 주로 사용하던 기술로 보통 테이블과 객체 모델<br/>
* (모든 field가 String 이거나, Map 객체인 경우가 대부분)이 1:1로 매칭된 형태로,<br/>
* 여러 외부 요건에 의해서 수정을 하려고 하면, 
* biz가 변경되면 쿼리를 mapping된 sql를 수정하고, biz 로직을 수정하고...<br/>
* 여튼 무지 수정을 해야 하는 절차 지향형 스타일 코드가 대량 생산된다.

## Domain Model
* JPA 기술등을 활용하여 DB 설계와 도메인 객체들과의 불일치를 어느정도 해결 하여 <br/> 
* 데이터 기반 설계가 아닌, 상호 영향을 받는 domain들과의 관계를 설계하는 방식으로<br/>
* 쉽게 애기 해서, 무지 수정하지 말고, 관련된 domain에 할당된 책임들만 손보면 되도록<br/>
* 역활과 책임을 적절히 분리한 형태 


## 위 사항 중 무엇 선택해야 하나?

> 변하지 않는 것은 ___"모든 것이 변한다."___는 사실 뿐이다.<br/> 
> -- 헤라클레스 토스

> (도메인 모델은) ___복잡성을 알고리즘에서 분리하고 객체간의 관계로 만들수 있다.___<br/>
> 유효성 검사, 계산, 파생 등이 포함된 복잡하고, 끊임없이 변하는<br/>
> 비즈니스 규칙을 구현해야 한다면 객체 모델을 사용해 비즈니스 규칙을 처리하는 것이 현명하다.<br/>
> -- Martin Fowler

## CQRS(Command Query Responsibility Segregation) Pattern
상태를 변경하는 명령 기능과 내용을 조회하는 쿼리 기능을 위한 모델을 구분하는 패턴(DDD start p.246)
