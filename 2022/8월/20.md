# 블로그 포스팅 (작성중)

이번 프로젝트에서 사용자 인증/인가 처리를 구현하게 되었습니다.

Spring Security를 기반으로 네이버, 카카오, 구글 서버의 OAuth 2.0 로그인을 지원하며, 회원가입 및 폼 로그인은 사용하지 않았습니다.

# 기본 설정

### SecurityConfig.java

- 로그인 성공, 실패 핸들러와 JWT 토큰 검증을 위한 필터 등을 적용하여 내가 의도한 방식의 filterChain을 구성합니다.

```java
@RequiredArgsConstructor
@EnableWebSecurity
@Configuration
public class SecurityConfig {

    private final CookieAuthorizationRequestRepository cookieAuthorizationRequestRepository;
    private final OAuth2AuthenticationSuccessHandler oAuth2AuthenticationSuccessHandler;
    private final OAuth2AuthenticationFailureHandler oAuth2AuthenticationFailureHandler;
    private final CustomOAuth2UserService customOAuth2UserService;
    private final JwtAuthenticationEntryPoint jwtAuthenticationEntryPoint;
    private final JwtAccessDeniedHandler jwtAccessDeniedHandler;
    private final JwtAuthenticationFilter jwtAuthenticationFilter;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {

        http.cors()
            .and()
            .csrf().disable()
            .httpBasic().disable()
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);

        //접근권한
        http.authorizeRequests()
                .anyRequest().permitAll();

        http.formLogin().disable();

        //OAuth
        http.oauth2Login()
                .authorizationEndpoint()
                .authorizationRequestRepository(cookieAuthorizationRequestRepository) // 인가요청 시작부터 콜백시점까지 OAuth2AuthorizationRequest 정보를 유지시켜줌
                .and()
                .userInfoEndpoint()
                .userService(customOAuth2UserService)
                .and()
                .successHandler(oAuth2AuthenticationSuccessHandler)
                .failureHandler(oAuth2AuthenticationFailureHandler)
                .defaultSuccessUrl("/main");

        http.exceptionHandling()
                .authenticationEntryPoint(jwtAuthenticationEntryPoint)
                .accessDeniedHandler(jwtAccessDeniedHandler);

        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

    @Order(0)
    @Bean
    public SecurityFilterChain resources(HttpSecurity http) throws Exception {
        //example
        http.requestMatchers(matchers -> matchers.antMatchers("/resource/**"))
                .authorizeHttpRequests(authorize -> authorize.anyRequest().permitAll())
                .requestCache().disable()
                .securityContext().disable()
                .sessionManagement().disable();
        return http.build();
    }
}
```

# Redis 설정

- 리프레시 토큰의 임시 저장소로 redis 활용

### RedisConfig.java

```java
@EnableRedisRepositories
@Configuration
public class RedisConfig {

    @Value("${spring.redis.host}")
    private String host;

    @Value("${spring.redis.port}")
    private int port;

    @Bean
    public RedisConnectionFactory redisConnectionFactory() {
        return new LettuceConnectionFactory(host, port);
    }

    @Bean
    public RedisTemplate<String, String> redisTemplate() {
        RedisTemplate<String, String> redisTemplate = new RedisTemplate<>();
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new StringRedisSerializer());
        redisTemplate.setConnectionFactory(redisConnectionFactory());
        return redisTemplate;
    }
}
```

### RedisService.java

```java
@RequiredArgsConstructor
@Service
public class RedisService {

    private RedisTemplate<String, String> redisTemplate;

    public void setValues(String key, String value) {
        ValueOperations<String, String> values = redisTemplate.opsForValue();
        values.set(key, value);
    }

    public void setValues(String key, String value, Duration duration) {
        ValueOperations<String, String> values = redisTemplate.opsForValue();
        values.set(key, value, duration);
    }

    public String getValues(String key) {
        ValueOperations<String, String> values = redisTemplate.opsForValue();
        return values.get(key);
    }

    public void deleteValues(String key) {
        redisTemplate.delete(key);
    }
}
```

# OAuth2User

### OAuth2Attribute.java

- 카카오, 네이버, 구글 3가지 종류의 OAuth2User 정보를 해석하여 동일한 유형의 Entity로 변환해주는 역할을 한다.

```java
@Getter
public class OAuthAttributes {

    private String oauthId;
    private String username;

    @Builder
    public OAuthAttributes(String oauthId, String username) {
        this.oauthId = oauthId;
        this.username = username;
    }

    public static OAuthAttributes of(String provider, String userNameAttributeName, Map<String, Object> attributes) {
        if (provider.equals("google")) returnofGoogle(attributes,createAuthId(attributes, provider, userNameAttributeName));
        else if (provider.equals("kakao")) returnofKakao(attributes,createAuthId(attributes, provider, userNameAttributeName));
        else if (provider.equals("naver"))  returnofNaver(attributes,createAuthId(attributes, provider, userNameAttributeName));
        else throw new IllegalArgumentException("유효하지 않은 provider 타입 입니다.");
    }

    //구글
    private static OAuthAttributes ofGoogle(Map<String, Object> attributes, String authId) {
        return OAuthAttributes.builder()
                .oauthId(authId)
                .username((String) attributes.get("name"))
                .build();
    }

    //카카오
    private static OAuthAttributes ofKakao(Map<String, Object> attributes, String authId) {
        Map<String, Object> account = (Map<String, Object>) attributes.get("kakao_account");
        Map<String, Object> profile = (Map<String, Object>) account.get("profile");

        return OAuthAttributes.builder()
                .oauthId(authId)
                .username((String) profile.get("nickname"))
                .build();
    }

    //네이버
    private static OAuthAttributes ofNaver(Map<String, Object> attributes, String authId) {
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");

        return OAuthAttributes.builder()
                .oauthId(authId)
                .username((String) response.get("name"))
                .build();
    }

    public static String createAuthId(Map<String, Object> attributes, String provider, String userNameAttributeName) {
        // naver만 규칙이 다름
        if (provider.equals("naver")) {
            Map<String, Object> tmpResponse = (Map<String, Object>) attributes.get("response");
            return provider + "_" + tmpResponse.get("id");
        }
        else return provider + "_" + attributes.get(userNameAttributeName);
    }

    public User toEntity() {
        return User.builder()
                .oauthId(this.oauthId)
                .username(this.username)
                .build();
    }
}
```

### CustomOAuth2User.java

- 인증객체

```java
@Data
public class CustomOAuth2User implements OAuth2User, UserDetails {

    private Long id;
    private String username;
    private String password;
    private Collection<? extends GrantedAuthority> authorities;
    private Map<String, Object> attributes;

    @Builder
    public CustomOAuth2User(Long id, String username,String password, Collection<? extends GrantedAuthority> authorities) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.authorities = authorities;
    }

    public static CustomOAuth2User create(User user) {
        Collection<GrantedAuthority> collect = new ArrayList<>();
        collect.add(() -> user.getRole().toString());

        return CustomOAuth2User.builder()
                .id(user.getId())
                .username(user.getUsername())
                .authorities(collect)
                .build();
    }

    @Override
    public Map<String, Object> getAttributes() {
        return this.attributes;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return authorities;
    }

    @Override
    public String getUsername() {
        return username;
    }

    @Override
    public String getName() {
        return String.valueOf(id);
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }

}
```

### CustomOAuth2UserService.java

- ClientRegistration과 유저 정보를 토대로 User 객체를 생성
- DB를 조회하여 기존 유저, 신규 유저에 따른 이후 로직 실행 (추가 예정)
- 

```java
@Service
@RequiredArgsConstructor
public class CustomOAuth2UserService extends DefaultOAuth2UserService {

    private static final Loggerlog= LoggerFactory.getLogger(CustomOAuth2UserService.class);
    private final UserService userService;
    private final UserRepository userRepository;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
        //null 체크
        Assert.notNull(userRequest, "null request");

        Map<String, Object> attributes = super.loadUser(userRequest).getAttributes();

        // OAuth 서버 고유 id값
        String userNameAttributeName = userRequest.getClientRegistration().getProviderDetails().getUserInfoEndpoint().getUserNameAttributeName();

        //getRegistrationId (kakao, naver, google)
        String provider = userRequest.getClientRegistration().getRegistrationId();

        //create User entity
        OAuthAttributes attribute = OAuthAttributes.of(provider,userNameAttributeName,attributes);
        User user = attribute.toEntity();

        //check oldUser
        Optional<User> oldUser = userRepository.findByOauthId(user.getOauthId());

        //saveOrUpdate create CustomOAuth2User
        CustomOAuth2User oAuth2User;
        if(oldUser.isPresent()) {
            userService.saveOrUpdate(oldUser.get());
log.info("update oldUser");
            oAuth2User = CustomOAuth2User.create(oldUser.get());
        }
        else {
            userService.saveOrUpdate(user);
log.info("save newUser");
            oAuth2User = CustomOAuth2User.create(user);
        }
log.info(oAuth2User.toString());
        //return OAuth2User
        return oAuth2User;
    }
}
```