# 오늘 배운점

---

```java
public class OAuthAttributes {

    private String name;
    private String email;

    @Builder
    public OAuthAttributes(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public static OAuthAttributes of(String provider, Map<String, Object> attributes) {
        if (provider.equals("google")) returnofGoogle(attributes);
        else if (provider.equals("kakao")) returnofKakao(attributes);
        else if (provider.equals("naver")) returnofNaver(attributes);
        else throw new IllegalArgumentException();
    }

    //구글
    private static OAuthAttributes ofGoogle(Map<String, Object> attributes) {
        return OAuthAttributes.builder()
                .name((String) attributes.get("name"))
                .email((String) attributes.get("email"))
                .build();
    }

    //카카오
    private static OAuthAttributes ofKakao(Map<String, Object> attributes) {
        Map<String, Object> account = (Map<String, Object>) attributes.get("kakao_account");
        Map<String, Object> profile = (Map<String, Object>) attributes.get("profile");

        return OAuthAttributes.builder()
                .name((String) profile.get("name"))
                .email((String) account.get("email"))
                .build();
    }

    //네이버
    private static OAuthAttributes ofNaver(Map<String, Object> attributes) {
        Map<String, Object> response = (Map<String, Object>) attributes.get("response");

        return OAuthAttributes.builder()
                .name((String) response.get("name"))
                .email((String) response.get("email"))
                .build();
    }
}

```

# 오늘 느낀점

---

- 복습이 중요하다!

# 내일 목표

---

- 일찍 잠들기!