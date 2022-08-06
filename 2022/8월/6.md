# 오늘 배운점

---

# SSO(Single Sign On)

- 단일 로그인, 즉 한 번의 로그인으로 여러개의 애플리케이션을 이용할 수 있는 서비스
- 일반적으로는 사용하고자하는 서비스마다 각각의 계정을 가지고 있어야 하지만, SSO를 도입한다면 서비스마다 개별로 계정을 만들지 않고, 하나의 계정으로 해당 서비스를 이용할 수 있게 된다.

### SSO의 대표적인 구현 방식

- SAML (Security Assertion Markup Language)
    - 인증 정보 사용자와 서비스 제공자 간의 인증 및 인가 데이터를 교환하기 위한 XML 기반의 표준 데이터 포맷
    - XML 형식이라 브라우저를 통해서만 동작이 가능하므로 모바일이나 Native App에는 적합하지 않다.
- OAuth (Open Authorization)
    - Authorization을 위한 개방형 표준 프로토콜
    - 리소스 소유자를 대신하여 리소스 서버에서 제공하는 자원에 대한 접근 권한을 위임하는 방식을 제공
    - SAML의 단점을 보완하기 위해 등장했고, JSON을 기반으로 한다.
    - Access Token을 통해 리소스에 접근할 수 있으며, redirect uri를 지정하여 등록된 uri에만 응답결과를 보낼 수 있도록 한다.
    - 하지만 Access Token 자체는 암호화 되지 않았기 때문에 탈취당할 위험이 있다.
- OIDC (OpenID Connect)
    - OAuth 2.0을 이용하여 만들어진 인증을 위한 레이어
    - OAuth로 발급받은 Access Token은 일시적으로 특정 권한을 허가해준 토큰일 뿐, 사용자에 대한 정보는 담고있지 않다. 따라서 인증을 위해 활용되면 안된다. 그래서 OIDC에서는 인증을 위해 ID Token을 추가했다.
    - ID Token은 JWT 형태이며, claim들을 통해 발행자와 사용자 정보를 알 수 있다.
    

### 정리

- OAuth는 인가를 위한 프로토콜이기 때문에 발급받은 Access Token을 인증을 위한 수단으로 활용해서는 안됨.
- Access Token을 통해 특정 자원에 접근할 수 있으니 인증의 도구로 생각될 수 있으나, 탈취 가능성 및 다른 클라이언트의 Access Token으로 로그인 위장 등의 보안 위협이 존재하다고 함
- 참고 블로그 : [https://gruuuuu.github.io/security/ssofriends/](https://gruuuuu.github.io/security/ssofriends/)

## Spring security oauth2 관련 yaml 설정

```yaml
spring:
	security:
	  oauth2:
	    client:
	      registration:
	        google:
	          client-id: id
						client-secret: secret
	          scope: email
	        kakao:
	          client-id: id
	          redirectUri: "http://localhost:8080/login/oauth2/code/kakao"
	          client-authentication-method: POST
	          authorization-grant-type: authorization_code
	          scope: account_email, gender
	          client-name: Kakao
	        naver:
	          client-id: id
	          client-secret: secret
	          redirect-uri: "http://localhost:8080/login/oauth2/code/naver"
	          authorization-grant-type: authorization_code
	          scope: name, email, gender, mobile
	          client-name: Naver
	      provider:
	        kakao:
	          authorization_uri: https://kauth.kakao.com/oauth/authorize
	          token_uri: https://kauth.kakao.com/oauth/token
	          user-info-uri: https://kapi.kakao.com/v2/user/me
	          user_name_attribute: id
	        naver:
	          authorization_uri: https://nid.naver.com/oauth2.0/authorize
	          token_uri: https://nid.naver.com/oauth2.0/token
	          user-info-uri: https://openapi.naver.com/v1/nid/me
	          user_name_attribute: response
```

- ClientRegistration : OAuth2.0이나 OpenID Connect 1.0 Provider에 등록한 클라이언트를 의미하는 클래스
    - 클라이언트 ID, secret, grant type, redirect uri 등 상세 정보를 가지고 있음.
    - 정의된 프로퍼티
    
    ```java
    public final class ClientRegistration {
        private String registrationId;  // (1)
        private String clientId;    // (2)
        private String clientSecret;    // (3)
        private ClientAuthenticationMethod clientAuthenticationMethod;  // (4)
        private AuthorizationGrantType authorizationGrantType;  // (5)
        private String redirectUriTemplate; // (6)
        private Set<String> scopes; // (7)
        private ProviderDetails providerDetails;
        private String clientName;  // (8)
    
        public class ProviderDetails {
            private String authorizationUri;    // (9)
            private String tokenUri;    // (10)
            private UserInfoEndpoint userInfoEndpoint;
            private String jwkSetUri;   // (11)
            private Map<String, Object> configurationMetadata;  // (12)
    
            public class UserInfoEndpoint {
                private String uri; // (13)
                private AuthenticationMethod authenticationMethod;  // (14)
                private String userNameAttributeName;   // (15)
    
            }
        }
    }
    ```
    
    (1) - ClientRegistration을 식별할 수 있는 유니크한 ID (google, kakao, naver)
    
    (6) - 클라이언트에 등록한 리다이렉트 URI로, 사용자 인증으로 클라이언트에 접근 권한을 부여하고 나면, 인가 서버가 해당 URI로 최종 사용자의 user-agent를 리다이렉트 시킴
    
    (7) - 인가 요청 플로우에서 클라이언트가 요청한 openid, 이메일, 프로필 등의 scope
    
    (9) - 인가 서버의 인가 endpoint URI
    

# 오늘 느낀점

---

- 이번주는 휴가 때문인지 공부량이 적었던 것 같다. 다음주 부터는 다시 일상으로 돌아와서 빡세게 달려야지!

# 내일 목표

---

- 팀 멘토링
- 로그인