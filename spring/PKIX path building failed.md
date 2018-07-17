## PKIX path building failed

RestTemplate으로 다음과 같이 https url에 접근시 에러가 발생하는 경우


https 서버와 통신해야 하는 상황에서 인증서 없이  HttpsURLConnection 을 사용하면 다음과 같이 SSLHandshakeException이 발생한다.


> javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target


위의 문제를 해결 하기 위해서 InstallCert.java 파일을 사용하여, jssecacerts를 생성해야 한다.

> javac InstallCert.java
> java InstallCert {{https 주소1.1.1.1}}:{{ssl part 443}}

위의 명령어로 정상적으로 jssecacerts파일이 생성되면 다음과 같은 경로에 복사 한다.
> sudo cp jssecacerts /Library/Java/JavaVirtualMachines/jdk1.8.0_131.jdk/Contents/Home/jre/lib/security
