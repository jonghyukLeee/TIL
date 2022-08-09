# 오늘 배운점

---

## YAML binding to Map

- YAML

```yaml
app:
  auth:
    tokenSecret: {secret}
    tokenExpiration: 3600000
  oauth2:
    redirectUri : "http://localhost:8080/oauth2/redirect"
```

- Config

```java
@Getter
@ConfigurationProperties(prefix = "app")
@Component
public class AppConfig {

    private final Auth auth = new Auth();
    private final OAuth2 oauth2 = new OAuth2();

    @Getter
    @NoArgsConstructor(access = AccessLevel.PROTECTED)
    public static class Auth {
        private String tokenSecret;
        private long tokenExpiration;

        @Builder
        public Auth(String tokenSecret, long tokenExpiration) {
            this.tokenSecret = tokenSecret;
            this.tokenExpiration = tokenExpiration;
        }
    }

    @Getter
    @RequiredArgsConstructor
    public static final class OAuth2 {
        private String redirectUri;
    }
}
```

- yaml 설정 파일을 바인딩하여 위와같은 형태로도 활용할 수 있다.
- @Value 로만 사용했었는데, 다른 방식도 적용해보았다.

### ResponseCookie

- samesite옵션
    - Strict : 서로 다른 도메인에서는 아예 전송 불가능. CSRF를 100% 방지할 수 있지만, 사용자 편의성을 많이 해칠 수 있음
    - Lax : Strict 설정에서 (GET method, a href, link href)만 예외
    - None : 동일 사이트와 크로스 사이트 모두 쿠키 전송이 가능

### Filter와 **OncePerRequestFilter**

- Filter와 GenericFilterBean
    - GenericFilterBean
        - Filter를 확장하여 스프링에서 제공하는 필터로, 스프링 위에서 동작하기 때문에 스프링의 설정 정보를 가져올 수 있게 확장된 추상 클래스이다.
    - 이 두 필터는 매 서블릿마다 호출되기 때문에, 서블릿 실행 과정에서의 요청을 처리하면서, 앞서 거쳐온 필터들을 다시한번 거치게 되는 중복 현상이 발생할 수 있다.
    - 이러한 자원 낭비를 배제하기 위해 사용되는 필터가 `OncePerRequestFilter`
- **OncePerRequestFilter**
    - 이 추상 클래스를 구현한 필터는 사용자의 한 번의 요청에 대해 딱 한번만 실행되는 필터를 제공함으로써, 모든 서블릿에 일관된 요청을 처리하게 해준다.

# 오늘 느낀점

---

- 개발하면서 모르던 지식이나 구현해 봤지만 모호했던 지식들을 채워나가는 과정이 너무 재밌는 것 같다. 항상 느끼는거지만 이론보다는 체득하는게 확실히 도움 되는 것 같다. 오늘 공부는 너무 유익했다!

# 내일 목표

---

- 팀 회의
- **풋살**