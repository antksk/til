# DDD(Domain Driven Design)
도메인에 집중하고, ___도메인의 개념을 커뮤니케이션과 코드로 부드럽게 연결 시키려는 과정___이 DDD의 핵심!!!


* 참고 : 
  - 조영호 님의 애플리케이션 [애플리케이션 아키텍쳐와 객체지향][애플리케이션 아키텍쳐와 객체지향] 내용
  - [도메인 주도 설계의 본질][도메인 주도 설계의 본질] 
[애플리케이션 아키텍쳐와 객체지향]: http://www.slideshare.net/baejjae93?utm_campaign=profiletracking&utm_medium=sssite&utm_source=ssslideview
[도메인 주도 설계의 본질]: http://www.slideshare.net/baejjae93/ss-27536729


* 용어 :
  - Entity : 식별성을 지닌 영속 객체(JPA의 @Entity를 가진 객체) 
  - Value Object : 식별성이 아닌 속성을 이용해 정의 되는 __불변객체__(JPA에서 @Embedded로 정의된 객체)
  - Aggregate : 연관된 Entity와 Value Object의 묶음, 트랜잭션 의 분산 단위, 캡슐화를 통한 복잡성 관리
  - Factory : 생성하기 복잡한 Aggregate내의 여러 객체들을 동시에 생성하는 역활을 담당하는 객체로 Aggregate의 일관성 유지
  - Bounded Context : 특정한 Domain Model이 적용되는 제한된 영역, 경계내에선 동일한 모델을 일관되게 적용, 경계 밖의  욀관성은 고려 대상이 아님
  - Service : Domain Object에 위치시키기 어려운 오퍼레이션을 가지는 객체, 여러 Domain Object를 다루는 연산, Service의 오퍼레이션은 일반적으로 상태를 가지지 않음(stateless)

  
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