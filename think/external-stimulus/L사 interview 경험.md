## 0차 서류 전형
* L사 사이트 서류 전형

## 1차 코딩 테스트
특이 사항 : 입력 처리에 대해 직접 구현해야 함
- 메모리 카피
- Stack 구현
- Queue 구현
- 사각형 넓이 구하기
```java
@Slf4j
public class memcpy_Test {


    private static void memcpy(short[] v, int dest, int src, int size) {
        if( (src > size) || (0 == src && 0 == size) ){
            return;
        }

        short[] buf = new short[(src+size)];
        int bufItor = 0;

        for( int i = src; src+size-1 > i; ++i ){
            buf[bufItor++] = v[i];
        }

        bufItor = 0;
        for( int i = dest; dest+size-1 > i; ++i ){
            v[i] = buf[bufItor++];
        }
    }


    @Test
    public void test() {

        short[] v = new short[]{0, 255, 123, 12, 2, 4, 12, 4, 55, 2, 33, 1, 4, 22, 5, 44, 89, 29, 31, 55};

        memcpy(v,5, 0, -1);

        for( int i = 0; v.length > i; ++i ){
            System.out.print(v[i]);
            if( v.length - 1 != i ){
                System.out.print(" ");
            }
        }
    }
}

@Slf4j
public class Queue_Test {

    public static class Queue {


        private ArrayList<Integer> datas;

        public Queue() {
            datas = new ArrayList<Integer>();
        }

        public boolean isEmpty() {
            return datas.isEmpty();
        }


        public Integer remove(){
            return datas.remove(0);
        }

        public boolean add(int value){
            return datas.add(value);
        }

        public int min() {
            int min = Integer.MAX_VALUE;
            for (Integer data : datas) {
                min = Math.min(min,data);
            }
            return min;
        }
    }


    private static Queue queue = new Queue();

    static final Pattern addRex = Pattern.compile("add (\\d+)");
    static final Pattern minRex = Pattern.compile("min");
    static final Pattern removeRex = Pattern.compile("remove");


    public static void addOperation(String input) {
        final Matcher add = addRex.matcher(input);
        if (add.matches()) {
            queue.add(Integer.parseInt(add.group(1)));
        }
    }

    @Test
    public void test() {
        addOperation("add 5");
        addOperation("add 2");
        addOperation("add 6");


        log.debug("{}", queue.min());
        log.debug("{}", queue.remove());

    }
}
```
