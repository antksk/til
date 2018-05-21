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
## 3-3차 