#JWT(JSON Web Tokens)
* 참고 자료 :
	- [JSON Web Tokens][jwt-io]
	- [outsider님의 JWT 설명 내용][outsider-jwt]
	

jwt는 session이나, db에 client 고유 정보를 저장하지 않고, 

client 환경에 맞게 client 컴퓨터에 저장하기 위해 고안된 방법임

기존에는 SAML등을 이용해서 비슷한 형태로 저장하였으나,
 
xml 기반으로 설정되어 표현식이 복잡하여 정보를
 
트래픽이 커지는 문제가 있었는데 이를 개선하기 위해 json 형식으로 구현된 형태임 

### 구조 :
형식은 "."으로 구분하여 3단계로 나눠져 있음

1. header: 암호화 형식
2. body: 실제 저장될 정보가 저장곳(claim set이라고도 부름) key, value 형태로 구성됨
	 * iss(issuer) : 토근을 발급한 발급자
	 * sub(subject) : claim 의 주요 내용(subject)
	 * aud(audience): 현재 발급된 jwt를 사용할 수신자 정보
	 * exp(expiration time) : 토근 만료 시간
	 * nbf(Not Before): 지정된 시간 이전에는 처리 않도록 설정할 때 사용
	 * iat(issued At): 토근이 발급된 시간
	 * jti(jwt id): 토근 고유 식별자(ID)
3. signature: body에 대한 변조 검증을 위한 영역으로

body에 정의된 고유 토근이 있으며, 임의로 server와 client에 맞게 claim을 설정 추가 해서 작업하면 됨 

#### 주의할 점
header, body는 실제로 암호화 되는 영역이 아니기 때문(단순히 base64로 인코딩 처리됨)에 누구나 토근에 대한 정보를 열람할수 있음 

[outsider-jwt]: https://blog.outsider.ne.kr/1160 
[jwt-io]: https://jwt.io/

#JaSYpt(Java Simplified Encryption)
* 참고 자료 :
	- [jasypt에 대한 설명][jasypt]
	- [jasypt spring boot 구현체 설명][jasypt-spring-boot]

암호화에 대한 전문 지식이 없어도, 단순하게 암호화 처리가 가능하도록 도와주는 라이브러리

### 테스트 : 
```java

StandardPBEStringEncryptor pbeEnc = new StandardPBEStringEncryptor();
pbeEnc.setPassword("test"); // 암호화 키
pbeEnc.setAlgorithm("PBEWithMD5AndDES"); // 암호화 알고리즘

//암호화 할때 마다 결과 값이 계속 다르게 나옴
log.debug("test encrypt: {}",  pbeEnc.encrypt("암호화 진행") );
log.debug("test decrypt: {}",  pbeEnc.decrypt("SVvj3BCILiZdzWru4W35Ljf46YF3KNIzQL2DPv3wAQQ="));
```

### 출력 결과 : 
```
[2016-11-11 16:55:20] test encrypt: SVvj3BCILiZdzWru4W35Ljf46YF3KNIzQL2DPv3wAQQ=
[2016-11-11 16:55:20] test decrypt: 암호화 진행
```

jasypt-spring-boot-starter를 설치하면 spring boot에서 손쉽게 환경파일(application.yml등과 같은)의 특정 요소를 암호화 할수 있음 

### 설치 : 
```gradle
// Java simplified encryption(jasypt)
// https://mvnrepository.com/artifact/com.github.ulisesbocchio/jasypt-spring-boot
compile("com.github.ulisesbocchio:jasypt-spring-boot-starter:1.9")
```

### 설정 : 
```java
@SpringBootApplication
@EnableEncryptableProperties // 기본 환경 설정 파일에 ENC로 시작하는 요소 발견시 복호화 처리함
public class Application {
  public static void main(String[] args) {
    SpringApplication.run(NvmSecurityApplication.class, args);
  }
}
```

### 적용 : 
```yml
# application.yml 설정
# jasypt-spring-boot-starter 설치로 appliation.yml에 환경설정 
# 참고 : https://github.com/ulisesbocchio/jasypt-spring-boot#encryption-configuration
jasypt:
  encryptor:
    password: test
    algorithm: PBEWithMD5AndDES


# 같은 application.yml 파일에 암호화 적용 예시
security:
  secret-key: ENC(oS0gNe9XATzUCJI8XuL5lQ5y3k56QBaZ) 
  service-key: ENC(BhmXCUq1wrOlqZRNVKZU+ejzSzUQf2QHuahxc78pXuM=)
```


[jasypt]: http://www.jasypt.org/
[jasypt-spring-boot]: https://github.com/ulisesbocchio/jasypt-spring-boot
[jasypt-spring-boot-config]: https://github.com/ulisesbocchio/jasypt-spring-boot#encryption-configuration

