# JAVA8 시간 관련 객체에 대한 소계
참고 :
* [NAVER D2-Java의 날짜와 시간 API][NAVER D2-Java의 날짜와 시간 API]
* [Java8 in Action][Java8 in Action]
[NAVER D2-Java의 날짜와 시간 API]: http://d2.naver.com/helloworld/645609
[Java8 in Action]: http://www.yes24.com/24/Goods/17252419?Acode=101


## 날짜(LocalDate), 시간(LocalTime)
- Temporal 인터페이스를 상속 받아 구현됨
- Immutable 객체임(마치 String 처럼)
- LocalDate와 LocalTime은 사람이 읽기 쉬운 표현식을 사용하여 날짜와 시간을 다루는 class임
```java
LocalDate date = LocalDate.of(2014, 3, 18);
int year = date.getYear(); // 2014(년)
Month month = date.getMonth(); // MARCH (월)
int day = date.getDayOfMonth(); //18 (일)
DayOfWeek dow = date.getDayOfWeek(); // TUESDAY (요일)
int len = date.lengthOfMonth(); // 31 (3월의 마지막 일)
boolean leap = date.ifLeapYear(); // false (윤년인지 여부 boolean으로 리턴)

LocalDate today = LocalDate.now(); // 시스템 시계를 통해 현재 날짜 정보를 얻어옴
```

- TemporalField : 시간 관련 객체에서 어떤 필드이 값에 접근할지 정의하는 인터페이스
- ChronoField : TemporalField 인터페이스를 정의한 열거자(enum)

```java
LocalDate date = LocalDate.of(2014, 3, 18);
// ChronoField 열거형을 이용한 표현
int year = date.get(ChronoField.YEAR);  // 2014(년)
int month = date.get(ChronoField.MONTH_OF_YEAR); // 3(월)
int day = date.get(ChronoField.DAY_OF_MONTH); //18(일)
```
- LocalTime 클래스로 시간을 표현함, (시간, 분), (시간, 분, 초), (초)등을 인수로 받는 오버로드된 of 메소드를 제공함

```java
LocalTime time = LocalTime.of( 13, 45, 20); // 13:45:20 (오후 1시 45분 20초)

int hour = time.getHour(); // 13 (시)
int minute = time.getMinute(); // 45 (분)
int second = time.getSecond(); // 20 (초)
```

- 날짜와 시간 문자열을 통한 LocalDate와 LocalTime 생성
```java
LocalDate date = LocalDate.parse(“2014-03-18”);
LocalTime time = LocalTime.parse(“13:45:20”);
```
parse 메소드에 DateTimeFormatter를 추가로 전달하여, 날짜, 시간 형식을 지정할수 있음, 문자열로 부터 LocalDate, LocalTime객체를 생성하지 못하면, DateTimeParseException(RuntimeException를 상속받은 예외)가 발생함


## 날짜와 시간 조합( LocalDateTime )
```java
// 2014-03-18T13:45:20

LocalDateTime dt1 = LocalDateTime.of(2014, Month.MARCH, 18, 13, 45, 20);
LocalDateTime dt2 = LocalDateTime.of(date,time);
LocalDateTime dt3 = date.atTime(13, 45, 20);
LocalDateTime dt4 = date.atTime(time);
LocalDateTime dt5 = time.atDate(date);

// 현재 날짜 + 시간 출력
LocalDateTime dt = LocalDateTime.now();
LocalDate date = dt.toLocalDate();
LocalTime time = dt.toLocalTime();
```


## 날짜 포맷
http://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html
```java
LocalDate date = LocalDate.now();
String text = date.format(formatter);
LocalDate parsedDate = LocalDate.parse(text, formatter)
```

## 날짜 범위 지정을 통한 리스트 반환
```java
final LocalDateTime startDate = LocalDateTime.now();
final LocalDateTime endDate = startDate.plusMonths(1);
final long betweenLimitDays = ChronoUnit.DAYS.between(startDate, endDate) + 1;

List<LocalDate> rangeDays = Stream.iterate(startDate, d->d.plusDays(1))
	.limit(betweenLimitDays)
	.map(d->d.toLocalDate())
	.collect(Collectors.toList())
;
log.debug("{}", rangeDays);

```

## 시간 범위 테스트
```java
@Slf4j
public class LocalTimeTest {

    @Value
    static class LocalTimeBoundChecker{
        private LocalTime fromTime;
        private LocalTime toTime;


        public static LocalTimeBoundChecker of(LocalTime fromTime, LocalTime toTime){
            return new LocalTimeBoundChecker(fromTime, toTime);
        }

        public boolean isBoundTime(LocalTime targetTime){
            return isFromTimeBefore(targetTime) && isToTimeAfter(targetTime);
        }

        public boolean isFromTimeBefore(Temporal targetTime){
            return 0 <= Duration.between(fromTime, targetTime).getSeconds();
        }

        public boolean isToTimeAfter(Temporal targetTime){
            return 0 > Duration.between(toTime, targetTime).getSeconds();
        }
    }

    @Test
    public void test(){
        displayTimeChecker(LocalTime.of(7,55));
        displayTimeChecker(LocalTime.of(8,0));
        displayTimeChecker(LocalTime.of(8,1));
        displayTimeChecker(LocalTime.of(12,55));
        displayTimeChecker(LocalTime.of(16,32));
        displayTimeChecker(LocalTime.of(22,00));
        displayTimeChecker(LocalTime.of(22,32));
        displayTimeChecker(LocalTime.of(23,59));
    }

    private void displayTimeChecker(LocalTime targetTime) {
        LocalTime fromTime = LocalTime.of(8,00,00);
        LocalTime toTime = LocalTime.of(22,0,0);

        Duration fromDuration = Duration.between(fromTime, targetTime);
        Duration toDuration = Duration.between(toTime, targetTime);

        log.debug("({})from : ({})s, \t\t\t ({})to : ({})s ==> result : {}",
                fromTime, fromDuration.getSeconds(),
                toTime, toDuration.getSeconds(),
                LocalTimeBoundChecker.of(fromTime, toTime).isBoundTime(targetTime));
    }

}
```

## 전체 시간을 기준으로 차이 계산
```ChronoUnit``` 클래스의 ```between(Temporal start, Temporal end)``` 메서드를 사용, 리턴타입은 ```long```

| 클래스 | 설명 |
| --- | --- |
| ChronoUnit.YEARS | 전체 년 차이 |
| ChronoUnit.MONTHS | 전체 월 차이 |
| ChronoUnit.WEEKS | 전체 주 차이 |
| ChronoUnit.DAYS | 전체 일 차이 |
| ChronoUnit.HOURS | 전체 시간 차이 |
| ChronoUnit.SECONDS | 전체 초 차이 |
| ChronoUnit.MILLIS | 전체 밀리초 차이 |
| ChronoUnit.NANOS | 전체 나노초 차이 |

```java
LocalDate startDate = LocalDate.now();
LocalDate endDate = LocalDate.of(2016,5,5);

log.debug("{}",  ChronoUnit.DAYS.between(startDate, endDate));

LocalTime startTime = LocalTime.now();
LocalTime endTime = LocalTime.of(22,0);

log.debug("{}",  ChronoUnit.SECONDS.between(startTime, endTime));
```
