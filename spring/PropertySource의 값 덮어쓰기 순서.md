스프링 부트는 PropertySource의 값을 아래 우선순의에 따라 유연하게 덮어쓰기할 수 있도록 설계되어 있다.
1. 사용자 홈 디렉토리에 있는 ```~/.spring-boot-devtools.properties```
2. 테스트에서 ```@TestPropertySource``` 애너테이션을 선언한 경우
3. 테스트에서 ```@SpringBootTest#properties```애너테이션을 선언한 경우
4. 커맨드라인 실행 인자
5. ```SPRING_APPLICATION_JSON```으로 정의된 속성
6. ServletConfig초기화 파라미터
7. ServletContext 초기화 파라미터
8. ```java:comp/env```으로 설정한 JNDI속성
9. JVM파라미터 속성(```System.getProperties(“xxxx”)```)
10. 운영체제 환경변수클라우드 플랫폼에서 사용하는 경우 애플리케이션 속성 확장에 사용)
11. ```random.*``` 속성을 가진 ```RandomValuePropertySource```
12. 패키징된 jar 외부에서 선언한 프로파일 정의 속성(```application{profile}.properties``` 혹은 YML 형식)
13. jar 파일 내부에 있는 프로파일 정의 속성(```application{profile}.properties``` 혹은 YML 형식)
14. 패키징된 jar외부에서 선언한 애플리케이션 속성들(```application{profile}.properties``` 혹은 YML 형식)
15. jar파일 내부에 있는 애플리케이션 속성들(```application{profile}.properties``` 혹은 YML 형식)
16. ```@Configuration```클래스에 있는 ```@PropertySource```애너테이션
17. 기본 속성들(```SpringApplication.setDefaultProperties```)를 이용해서 정의한 경우
