## 0차 서류 전형
* LinkiedIn 서류 전형

## 1차 홈워크 24시(pass)
* 알고리즘 문제 1개, SOLID 적용해서 풀기
* 프로세스 문제 사이트 파싱

## 2차 전화 인터뷰 40~50분
* ArrayList vs LinkedList 차이점
* List, Set, Map 설명
* Proxy 객체 특징 설명
* Tomcat OutOfMemory 분석
* Thead 및 Immutable 객체에 대한 설명 


## 3-1차 
* temp 없이 swap 함수 구현하기
```java
import java.*;
 
public class SwapWithoutTemp {
    
    private static void usingArithmeticOperatorsVersion(int x, int y){
        x = x + y;  // x는 15
        y = x - y;  // y는 10변경 ( x --> y )
        x = x - y;  // x는 5변경 ( y --> x )
        log.debug("x = {}, y = {}", x, y);
    }
    
    private static void usingBitwiseXORVersion(int x, int y){
        x = x ^ y; // x는 15(1111)
        y = x ^ y; // y는 10(1010)변경 ( x --> y ) 10 
        x = x ^ y; // x는 5(0101)변경 ( y --> x )
        log.debug("x = {}, y = {}", x, y);
    }
 
    public static void main(String a[]){
        int x = 10, y = 5;
        
        // +, - 연산을 통한 swap
        usingArithmeticOperatorsVersion(x, y); 
        
        // XOR 연산을 통한 swap
        usingBitwiseXORVersion(x, y);
    }
}
```
## 3-2차 
* 랜덤하게 생성된 숫자 리스트 중 3개의 요소를 뽑아서 합이 0이 되는 횟수를 찾아 반환


## 3-3차
* nested list를 flat list로 변경
```json
input = [1,[2,3],[4,[5,6,[7,8]],9],10]
output = [1,2,3,4,5,6,7,8,9,10]
```
```java
public class IntegerFlatList {
    private final List<Object> result;
    
    public IntegerFlatList(){
        this.result = new ArrayList<>();
    }
    
    public void flatList(List<Object> nestedList){
        for( Object e : nestedList ){
            if( e instanceof Integer ){
                result.add( e );
            }else if( e instanceof List ){
                flatList((List)e);
            }
        }
    }
}
```

# 3차 (drop) 
- 소감 : 
    - 질문할 수 없이 답변만해야 하는 환경이 답답함
    - 간단히 애기 해서, 서울대 생들한테 애기 하는 느낌을 받음( 무색 무취 )
    - 깊이 있게 애기 하고 싶다는 생각이 들지 않음, 고로 최선을 다하지 않음, 모르겠다는 말을 더 많이 함
    - 면접 내내 재미 없음