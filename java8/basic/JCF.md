# Java Collection Framework (JCF)
 java에서 일련의 데이터들을 편하게 관리하기 위해서 만들어 놓은 기본적인 자료 구조

대표적인 상속 구조는 다음과 같음

![The core collection interfaces.](http://docs.oracle.com/javase/tutorial/figures/collections/colls-coreInterfaces.gif)


* [Collection][Collection]: JCF의 최상위 interface로 객체들의 그룹을 나타냄
  - [Set][Set]
  	- 동적인 일차원 배열을 생성(중복 형용되지 않음)
  	- index를 통한 탐색을 지원하며, 중복 없는 데이터를 저장할수 있음
  		- HashSet : 순서 없이 저장하지만 중복 제거에 용이한 클래스( HashMap기반으로 고정된 Object PRESENT를 싱글톤 형태의 값으로 가짐 )
        - TreeSet : 데이터를 담으면서 정렬(이진 트리 사용)하기 때문에 HashSet보다 느린 클래스
        - LinkedHashSet : 저장된 순서에 따라서 순서가 결정됨
        - SortedSet : 오름차순을 갖는 Set 인터페이스

  - [List][List]
  	- 동적인 일차원 배열을 생성
  	- index를 통한 탐색을 지원하며, 중복된 데이터를 저장할수 있음
  	    - Vector : 동기화 처리가 되어 있는 가변 배열
        - ArrayList : 동기화 처리 되지 않은 가변 배열
        - LinkedList : Queue인터페이스를 구현한 배열로 FIFO처리 가능
        
  - [Queue][Queue]
    - 처음 유입된 객체를 처음 처리 하는 방식(FIFO : First In First Out)으로 데이터를 처리 할 때 사용하는 인터페이스
        - PriorityQueue : 큐에 추가된 순서와는 상관없이 Comparator에 의해 정의된 순서로 먼저 나오도록 구성된 큐
        - LinkedBlockingQueue : 링크드 리스트 형태로 구현된 큐로 동기화 처리 되어 있음
        - ArrayBlockingQueue : 배열로 구현된 큐 동기화 처리됨
        - PriorityBlockingQueue : Comparator에 의해 정의된 순서로 먼저 나오도록 구성된 큐, 동기화 처리됨
        - DelayQueue : 큐가 대기하는 시간을 지정하여 처리하도록 구성된 큐
        - SynchronousQueue : put()메서드를 호출하면, 다른 스레드에서 take()메서드가 호출될 때 까지 대기하도록 되어 있는 큐
        - [Deque][Deque]
  
  
* [Map][Map]
    - key,vaule 형태의 데이터 구조를 나타냄
        - HashTable : 동기화 처리가 되어 있는 해쉬 테이블 클래스
        - HashMap : HashTable클래스와 다른점은 null값을 허용한 다는 것과 동기화되어 있지 않음
        - TreeMap : 키값에 의해 정렬 순서를 정함
        - LinkedHashMap : 이중 연결 리스트 방식으로 데이터를 담음
        - [SortedMap][SortedMap] : 키를 오름 차순으로 정렬하는 Map인터페이스


[Collection]: https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html
[Set]: https://docs.oracle.com/javase/8/docs/api/java/util/Set.html
[List]: https://docs.oracle.com/javase/8/docs/api/java/util/List.html
[Queue]: https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html
[Deque]: https://docs.oracle.com/javase/8/docs/api/java/util/Deque.html

[Map]: https://docs.oracle.com/javase/8/docs/api/java/util/Map.html
[SortedMap]: https://docs.oracle.com/javase/8/docs/api/java/util/Map.html