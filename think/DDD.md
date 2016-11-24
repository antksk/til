# DDD
* 참고 : 조용호 님의 애플리케이션 [애플리케이션 아키텍쳐와 객체지향][애플리케이션 아키텍쳐와 객체지향] 내용
[애플리케이션 아키텍쳐와 객체지향]: http://www.slideshare.net/baejjae93?utm_campaign=profiletracking&utm_medium=sssite&utm_source=ssslideview


* 용어 :
  - Entity : 식별성을 지닌 영속 객체(JPA의 @Entity를 가진 객체) 
  - Value Object : 식별성이 아닌 속성을 이용해 정의 되는 __불변객체__(JPA에서 @Embedded로 정의된 객체)
  - Aggregate : 연관된 Entity와 Value Object의 묶음, 트랜잭션 의 분산 단위, 캡슐화를 통한 복잡성 관리
  - Factory : 생성하기 복잡한 Aggregate내의 여러 객체들을 동시에 생성하는 역활을 담당하는 객체로 Aggregate의 일관성 유지
  