# 오늘 배운점

---

## 스프링 시큐리티 테스트 작성

- @WebMvcTest
    - 테스트 할 컨트롤러를 (controllers = {className}) 옵션으로 지정. @SpringBootTest 어노테이션은 스프링 부트에서 관리하는 모든 빈들을 생성한 후 테스트를 실행하기 때문에 불필요하게 많은 시간이 소요될 수 있다. 단위 테스트를 작성할 때는 이와 같은 어노테이션을 활용하여 웹과 관련된 빈들만 생성한다.
- @MockBeans, @MockBean
    - `@SpringBootTest`가 아닐 경우에는 테스트에 필요한 모든 빈들을 생성해주지는 않기 때문에 @MockBean 어노테이션을 통해 필요한 빈들을 목업해준다.
    
    ```java
    @MockBeans({
            @MockBean(JpaMetamodelMappingContext.class),
            @MockBean(PostService.class),
            @MockBean(CustomOAuth2AccountService.class)
    })
    ```
    
- MockMvc
    - 위 클래스를 통해 스프링 MVC의 동작을 재현할 수 있다.
    - 예시
    
    ```java
    @Test
    void posts() throws Exception {
        mvc.perform(get("/posts"))
                .andExpect(status().isOk()); //or andReturn()
    }
    ```
    
- @WithMockUser
    - 임의로 목업된 인증 유저를 사용하여 테스트 가능
    - default option
        - username = “user” , password = “password”, role = “USER”
    - role 옵션을 지정하게 되면 ROLE_ 이 prefix로 달림. authorites는 안달림.
    
    ```java
    @Test
    @WithMockUser(username = "guest", roles = {"GUEST"})
    void posts() throws Exception {
        mvc.perform(get("/posts"))
                .andExpect(status().isOk());
    }
    ```
    
    - 하지만, 위 방식은 `UsernamePasswordAuthenticationToken` 형태의 객체를 반환하기 때문에, Form 방식에서만 가능하다.
- oauth2Login()
    - `OAuth2AuthenticationToken` 반환
    - 권한 및 attributes도 정의할 수 있음.
    
    ```java
    @Test
    void posts() throws Exception {
    
        mvc.perform(get("/posts")
                .with(oauth2Login()
                        // 1
                        .authorities(new SimpleGrantedAuthority("ROLE_GUEST"))
                        // 2
                        .attributes(attributes -> {
                            attributes.put("username", "username");
                            attributes.put("name", "name");
                            attributes.put("email", "my@email");
                            attributes.put("picture", "https://my_picture");
                        })
                ))
                .andExpect(status().isOk());
    }
    ```
    
- @WithAnonymousUser
    - 인증되지 않은 사용자를 테스트할 때 활용
    - 클래스 단위에서 특정 테스트에만 미인증 사용자를 테스트 할 수 있다.
    - `AnonymousAuthenticationToken` 반환
- @WithSecurityContext
    - Custom OAuth2 인증 객체 생성을 위해 어노테이션을 만드는 방식
    - 어노테이션 생성
    
    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @WithSecurityContext(factory = WithMockCustomOAuth2AccountSecurityContextFactory.class)
    public @interface WithMockCustomOAuth2Account {
    
        String username() default "username";
    
        String name() default "name";
    
        String email() default "my@default.email";
    
        String picture() default "https://get_my_picture.com";
    
        String role() default "ROLE_USER";
    
    }
    ```
    
    - SecurityContext를 생성하는 Factory 구현체 생성
    
    ```java
    public class WithMockCustomOAuth2AccountSecurityContextFactory
        implements WithSecurityContextFactory<WithMockCustomOAuth2Account> {
    
        @Override
        public SecurityContext createSecurityContext(WithMockCustomOAuth2Account customOAuth2Account) {
    
            // 1
            SecurityContext context = SecurityContextHolder.createEmptyContext();
    
            // 2
            Map<String, Object> attributes = new HashMap<>();
            attributes.put("username", customOAuth2Account.username());
            attributes.put("name", customOAuth2Account.name());
            attributes.put("email", customOAuth2Account.email());
            attributes.put("picture", customOAuth2Account.picture());
    
            // 3
            OAuth2User principal = new DefaultOAuth2User(
                    List.of(new OAuth2UserAuthority(customOAuth2Account.role(), attributes)),
                    attributes,
                    customOAuth2Account.name());
    
            // 4
            OAuth2AuthenticationToken token = new OAuth2AuthenticationToken(
                    principal,
                    principal.getAuthorities(),
                    customOAuth2Account.registrationId());
    
            // 5
            context.setAuthentication(token);
            return context;
        }
    }
    ```
    
    - 테스트에 적용
    
    ```java
    @Test
    @WithMockCustomOAuth2Account(role = "ROLE_GUEST", registrationId = "google")
    void posts() throws Exception {
        mvc.perform(get("/posts"))
                .andExpect(status().isOk());
    }
    ```
    

# 오늘 느낀점

---

- 오늘 스터디 진행하면서 다른 스터디원들의 열정도 본받고, 비슷한 고민에 대한 다른 관점에서의 접근 방식도 생각해볼 수 있었다. 꾸준히 진행하고 싶다!

# 내일 목표

---

- 기획서 작성