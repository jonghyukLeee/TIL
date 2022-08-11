# 오늘 배운점

---

## AuthorizationRequestRepository

- Spring Security는 기본적으로 `**AuthorizationRequestRepository**`를 사용해 **Authorization** **Request**를 저장한다.
- Default 구현체는 **`HttpSessionOAuth2AuthorizationRequestRepository`**이며, **HttpSession**에 **OAuth2AuthorizationRequest**를 저장한다.

### Access Token / Refresh Token

- Access Token
    - 응답으로 보내서 클라이언트가 로컬변수로 사용
    - 유저 정보를 직접적으로 담고있음
    - 유저 정보를 담고있지만 토큰 방식 자체가 탈취에 취약하므로, 만료 기간을 짧게 두어 피해를 최소화
    - 요청시마다 헤더에 담아서 보내고, 서버는 해당 Access Token으로 검증
- Refresh Token
    - Access Token 만료시에 재발급을 위한 토큰으로 활용
    - 만료 기간이 길지만, 중요 정보는 담고있지 않음
    - 쿠키에 담아 전송
    - 서버에 토큰 정보를 저장해놓고, 검증시에 활용함

# 오늘 느낀점

---

- 최근 로그인을 구현하면서 JWT 토큰 방식에 대해 모호한 부분이 많았는데, 어제 오늘 깊은 이해를 한 것 같아서 조금 뿌듯하다. 까먹지 않으려면 기록을 해야하는데, 코드라도 남겨놔야겠다.

# 내일 목표

---

- 본 프로젝트에 OAuth 2.0 + JWT 적용