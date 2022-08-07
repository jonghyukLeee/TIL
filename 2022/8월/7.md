# 오늘 배운점

---

# Spring Security OAuth Configuration

### 세팅할 수 있는 모든 설정정보 예시

```java
@EnableWebSecurity
public class OAuth2LoginSecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
            .oauth2Login(oauth2 -> oauth2
                .clientRegistrationRepository(this.clientRegistrationRepository())
                .authorizedClientRepository(this.authorizedClientRepository())
                .authorizedClientService(this.authorizedClientService())
                .loginPage("/login")
                .authorizationEndpoint(authorization -> authorization
                    .baseUri(this.authorizationRequestBaseUri())
                    .authorizationRequestRepository(this.authorizationRequestRepository())
                    .authorizationRequestResolver(this.authorizationRequestResolver())
                )
                .redirectionEndpoint(redirection -> redirection
                    .baseUri(this.authorizationResponseBaseUri())
                )
                .tokenEndpoint(token -> token
                    .accessTokenResponseClient(this.accessTokenResponseClient())
                )
                .userInfoEndpoint(userInfo -> userInfo
                    .userAuthoritiesMapper(this.userAuthoritiesMapper())
                    .userService(this.oauth2UserService())
                    .oidcUserService(this.oidcUserService())
                    .customUserType(GitHubOAuth2User.class, "github")
                )
            );
    }
}
```

- OAuth 2.0 Login Page
    - loginPage 옵션은 기본적으로 DefaultLoginPageGeneratingFilter가 자동으로 생성해준다. 이 디폴트 로그인 페이지는 설정해둔 OAuth 클라이언트 설정파일의 ClientRegistration.clientName을 보여준다.
    - 해당 옵션을 설정하면 로그인 페이지를 재정의할 수 있다. 물론 해당 링크에 대한 컨트롤러 또한 정의해야함
    - 링크를 누르면 OAuth 로그인 및 인가 요청을 시작할 수 있다.
- Redirection Endpoint
    - 인가 서버가 리소스 소유자의 user-agent를 통해 가져온 인가 응답을 클라이언트에 전송할 때 사용한다.
    - OAuth 2.0 로그인은 기본값으로 인가 코드 부여 (Authorization Code Grant) 방식을 사용한다. 따라서 인가 credential은 인가 코드를 의미한다.
    - 이 옵션의 default값은 OAuth2LoginAuthenticationFilter.DEFAULT_FILTER_PROCESS_URI에 **/login/oauth2/code/**로 정의돼있다.
- UserInfo Endpoint
    - Mapping User Authorities
        - 사용자가 OAuth2.0 Provider로 인증을 마치고 나면, OAuth2User.getAuthorities()는 GrantedAuthority 인스턴스 셋으로 매핑되며, 인증을 완료할 때 OAuth2AuthenticationToken에 저장된다.
        - 이는 security의 인가 요청에 사용된다 (hasRole)
    - UserService
        - DefaultOAuth2UserService는 표준 OAuth 2.0 Provider를 지원하는 OAuth2UserService 구현체다.
        - OAuth2UserService는 UserInfo 엔드포인트에서 Access Token을 사용하여 최종 사용자의 속성을 받아오며, OAuth2User 타입의 AuthenticatedPrincipal을 리턴한다.
    - OpenID Connect 1.0 UserService
        - OidcUserService는 OpenID Connect 1.0 Provider를 지원하는 OAuth2UserService 구현체다.
        - OidcUserService는 DefaultOAuth2UserService로 UserInfo 엔드포인트로 사용자 속성을 요청한다.
        - ID Token Signature Verification
            - OIDC 인증에서 ID Token은 최종 사용자 인증에 대한 클레임을 담고 있는 보안 토큰으로, 인가 서버가 클라이언트에게 발급해준다.
            - OidcIdTokenDecoderFactory는 OidcIdToken 서명을 검증할 때 사용하는 JwtDecoder를 제공한다. 디폴트 알고리즘은 RS256이지만, 등록된 클라이언트에 따라 다를 수 있기 때문에 리졸버를 설정하면 바꿀 수 있음.
                - JWS 알고리즘 리졸버는 ClientRegistration을 받아 클라이언트 별로 원하는 알고리즘을 리턴하는 함수이다.
                - ex) 모든 ClientRegistration에 대해 MacAlgorithm.HS256을 리턴하는 빈객체
                
                ```java
                @Bean
                public JwtDecoderFactory<ClientRegistration> idTokenDecoderFactory() {
                    OidcIdTokenDecoderFactory idTokenDecoderFactory = new OidcIdTokenDecoderFactory();
                    idTokenDecoderFactory.setJwsAlgorithmResolver(clientRegistration -> MacAlgorithm.HS256);
                    return idTokenDecoderFactory;
                }
                ```
                
                - HS256, HS384, HS512같은 MAC 기반 알고리즘은 client-id에 상응하는 client-secret을 대칭키로 사용하여 서명을 인증한다.
                - OpenID Connect 1.0 인증을 사용하는 ClientRegistration을 여러 개 설정했다면, JWS 알고리즘 리졸버에서 건네받은 ClientRegistration을 확인해서 리턴할 알고리즘을 결정하면 된다.

## OAuth 2.0 + JWT

- JWT 방식을 활용하면 claim 형태로 사용자 속성및 토큰을 지니고 있기 때문에 요청마다 Auth Server에서 Access Token의 유효성을 검증해야 했던 불필요한 과정을 생략하고, 각 서버에서 앞선 과정을 수행할 수 있게 하여 비용 절감 및 Stateless 아키텍처를 구성할 수 있다.

### 인증 과정

1. 사용자가 소셜 로그인을 정상적으로 완료
2. `AbstractAuthenticationProcessingFilter`에서 OAuth2 로그인 과정을 호출
3. `Resource Server`에서 넘겨주는 정보를 토대로 `OAuth2LoginAuthenticationFilter`의 `attemptAuthentication()`에서 인증 과정을 수행
4. `attemptAuthentication()` 처리 과정에서 `OAuth2AuthenticationToken`을 생성하기 위해 `OAuth2LoginAuthenticationProvider`의 `authenticate()`를 호출
5. `authenticate()` 처리 과정에서 `OAuth2User`를 생성하기 위해 `OAuth2UserService`의 `loadUser()`를 호출

### StringBuilder

- 항상 문자열을 조합할 때, ‘+’ 연산으로 문자열을 더하면 뭔가 외관상 투박해 보이기도 하고 성능상 불리할까봐 StringBuilder를 활용했었다.
근데 그냥 ‘+’ 연산으로 더해줘도 컴파일러가 StringBuilder로 변환해서 합쳐준다고 한다. 
심지어 ‘+’ 연산이 훨씬 더 직관적이기 때문에 더이상 StringBuilder를 사용할 필요가 없는 것 같다.
- 하지만 반복문 내에서는 위처럼 변환되지 않는다고 하니 반복문에서는 이전처럼 Stringbuilder를 활용하기로 한다.

### **AWS Systems Manager Parameter Store**

- 데이터 및 암호를 계층적으로 관리할 수 있는 스토리지
- 값을 일반 텍스트 또는 암호화된 데이터로 저장할 수 있다.
- 데이터를 코드와 격리하여 보안 태세를 개선한다.

# 오늘 느낀점

---

- 팀 멘토링때 프로젝트 진행 속도가 더디다고 꾸중을 들었다. 팀원들과 속도를 맞추자는 의도였지만, 겉으로 봤을때는 좋아보이진 않다고 생각했다. 한명쯤은 쓴소리도 할줄 알아야 하는데, 다들 나서서 말하는 스타일이 아니라서 그런 것 같다!.. 내가해야지..

# 내일 목표

---

- AWS Systems Manager Parameter Store 를 활용한 키 관리 적용