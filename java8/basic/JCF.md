# Java Collection Framework (JCF)
 java에서 일련의 데이터들을 편하게 관리하기 위해서 만들어 놓은 기본적인 자료 구조

대표적인 상속 구조는 다음과 같음

![The core collection interfaces.](http://docs.oracle.com/javase/tutorial/figures/collections/colls-coreInterfaces.gif)


* [Collection][Collection]: JCF의 최상위 interface로 객체들의 그룹을 나타냄
  - [Set][Set]
  	- 동적인 일차원 배열을 생성
  	- index를 통한 탐색을 지원하며, 중복 없는 데이터를 저장할수 있음
  		- SortedSet
  - [List][List]
  	- 동적인 일차원 배열을 생성
  	- index를 통한 탐색을 지원하며, 중복된 데이터를 저장할수 있음
  - [Queue][Queue]쿠팡
  - [Deque][Deque]
* [Map][Map]: key,vaule 형태의 데이터 구조를 나타냄
	- [SortedMap][SortedMap]


[Collection]: https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html
[Set]: https://docs.oracle.com/javase/8/docs/api/java/util/Set.html
[List]: https://docs.oracle.com/javase/8/docs/api/java/util/List.html
[Queue]: https://docs.oracle.com/javase/8/docs/api/java/util/Queue.html
[Deque]: https://docs.oracle.com/javase/8/docs/api/java/util/Deque.html

[Map]: https://docs.oracle.com/javase/8/docs/api/java/util/Map.html
[SortedMap]: https://docs.oracle.com/javase/8/docs/api/java/util/Map.html