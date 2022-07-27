# 오늘 배운점

---

# OAuth 2.0

- oauth는 인증이 아닌 `인가`를 위한 프레임워크이다.
- oauth의 4요소
    - Resource Owner : 사용자
    - Client : 리소스에 접근하고자 하는 서비스
    - Authorization Server : 인가의 주체, 토큰 발행
    - Resource Server : 인가 서버의 토큰을 확인하여 리소스 제공
    - (Authorization Server / Resource Server)를 묶어서 OAuth Provider라고 표현하기도 함.
- 과정 순서 정리
    - 사용자는 Client 를 통해 로그인 접근을 시도하며, 클라이언트는 네이버, 카카오 등의 OAuth Provider를 통한 로그인 기능을 제공하고 있다.
    - 소셜 로그인을 정상적으로 완료하면 Authorization Server를 통해 토큰을 발급 받는다.
    - 해당 토큰으로 Resource Server에서 scope 범위 안에 있는 사용자 정보를 받아올 수 있다.

## OAuth 2.0 yml 설정

![12](https://user-images.githubusercontent.com/79312551/179721297-84af3786-505e-4c8f-9483-59477b403c27.png)

### 옵션 설명

- 등록 옵션 정보
    - scope : 리소스에 요청 가능한 범위 설정
    - authorization-grant-type : 권한 승인 타입 설정 (일반적으로 authorization_code 씀)
- provider
    - user_name_attribute : 응답 결과를 조회할 때 기준이되는 username을 명시.

## OAuth 2.0 + JWT

- OAuth 2.0 방식만 사용하게 되면 매 요청마다 토큰값을 체크해야하는 번거로움이 있고, 서버를 여러대 두거나 마이크로 서비스를 적용했을 경우 Auth 서버를 별도로 두어야 하기 때문에 부하로 이어질 수 있다.
- 따라서, JWT 방식을 추가로 적용한다.

### JWT

- 인증이 완료된 사용자에게 JWT 토큰을 발급한다
- 사용자는 매 요청마다 헤더에 해당 토큰을 담아 요청을 전송한다.
- 위 방법을 활용하게 되면, 서버가 분산되어 있더라도 JWT 토큰의 적합성을 인증할 방법만 알고있다면 인증 과정에 문제가 없다. 따라서 확장에 유리하다.

## 소마 프로젝트에 적용계획

- Naver, Google, Kakao 로그인
- 사용자는 해당하는 소셜 로그인을 진행함. (ID / PASSWORD 입력)
- 입력한 정보로 Authorization Server로 부터 토큰을 발행받음.
- 받은 토큰으로 OAuth Provider로 부터 지정한 scope 범위의 리소스를 전달받음.
- 이메일 정보를 선택한 provider에 맞게 수정한 후, DB에 등록(회원가입). 기존 사용자의 경우는 update
- OAuth 로그인을 정상적으로 마치더라도 JWT 토큰은 없는 상태이므로, 특정 URL로 리다이렉션 하여 토큰을 발급해준다.

# 오늘 느낀점

---

- 나는 깊게 공부하는 습관을 끊을수가 없나보다… 사소한 것 하나하나 그냥 넘어가지 못해서 계획했던 분량을 채우지 못했다 ㅎㅎㅎ..
- 평소 필기를 잘 하지 않아서, 그냥 공부만 하고 끝냈었는데, til을 작성해보니 공부 후에 정리하면서 복습하는 느낌이라 너무 좋은 것 같다. 꾸준히 해야지^^^

# 내일 목표

---

- 이펙티브자바 Enum 파트 공부하기
- 소연이랑 AWS 심화교육 집중해서! 굉장히 열심히 수강하기
