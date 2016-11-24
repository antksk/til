##[자주 까먹는 설정] gradle.porperties
시스템 보안 강화가 진행되면, 외부 인터넷이 연결되지 않은 곳에서 proxy를 통해, 특정 포트나 url만 접근해야 되는 경우가 발생함

이런 경우 gradle에 proxy설정을 통해 예외처리를 해야함

```porperties
# windows 10의 경우 
# C:\Users\{XXXXXXXXXX}\.gradle 디렉토리에 gradle.porperties 생성 하여 아래와 같이 등록
systemProp.http.proxyHost=xxx.xxxx.org or IP # 지정된 proxy ip 혹은 주소
systemProp.http.proxyPort=8080 # 예외 포트 
systemProp.http.proxyUser=userId # 지정된 id
systemProp.http.proxyPassword=password # 지정된 password
systemProp.http.nonProxyHosts=localhost|127.0.0.1:16105|etc....

systemProp.https.proxyHost=xxx.xxxx.org or IP # 지정된 proxy ip 혹은 주소
systemProp.https.proxyPort=8080 # 예외 포트
systemProp.https.proxyUser=userId # 지정된 id
systemProp.https.proxyPassword=password # 지정된 password
systemProp.https.nonProxyHosts=localhost|127.0.0.1:16105|etc....
```