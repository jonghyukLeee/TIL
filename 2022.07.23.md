# 오늘 배운점

---

### Jenkins

- JRE에서 동작
- 다양한 플러그인을 통해 자동화 작업을 처리할 수 있다.
- 일련의 자동화 작업의 순서들의 집합인 Pipeline을 통해 CI/CD 파이프라인을 구축한다.

### Jenkins의 대표 플러그인

- Credentials Plugin
    - Jenkins는 그냥 단지 서버이기 때문에 배포에 필요한 각종 리소스에 접근하기 위해서는 여러가지 중요한 정보들을 저장하고 있어야함.
    - 리소스에는 클라우드 리소스 혹은 베어메탈에 대한 ssh 접근 등을 의미한다.
    - AWS token, Git access token, secret key 등의 정보를 저장할 때 사용한다.
    - Jenkins는 private network에 떠있기 때문에 보안 문제가 많지 않음
    - Git Plugin은 Jenkins에서 git에 대한 소스코드를 긁어와서 빌드할 수 있도록 해줌.
    - Pipeline : 핵심 기능인 파이프라인 마저도 플러그인이다.
    - Docker plugin and Docker Pipeline : Docker agent를 사용하고 jenkins에서 도커를 사용하기 위한 플러그인

### Pipeline

- 파이프라인이란 CI/CD 파이프라인을 젠킨스에 구현하기 위한 일련의 플러그인들의 집합이자 구성
- 즉 여러 플러그인들을 이 파이프라인에서 용도에 맞게 사용하고 정의함으로써 파이프라인을 통한 서비스가 배포됨.
- DSL로 작성

### Pipeline의 Section

- Agent section
    - 젠킨스는 많은 일을 해야하기 때문에 혼자 하기 버겁다.
    - 여러 slave node를 두고 일을 시킬 수 있는데, 이처럼 어떤 젠킨스가 일을 하게 할 것인지를 지정한다.
    - 젠킨스 노드 관리에서 새로 노드를 띄우거나 혹은 docker 이미지를 통해 처리할 수 있다.
- Post section
    - 스테이지가 끝난 이후의 결과에 따라 후속 조치를 취할 수 있다.
    - 성공 시 이메일, 실패 시 중단 혹은 건너뛰기 등의 작업 결과에 따른 행동을 취할 수 있다.
- Stage section
    - 어떤 일들을 처리할 것인지 일련의 stage를 정의한다.
    - 일종의 카테고리
- Steps section
    - 한 스테이지 안에서의 단계로 일련의 스텝을 보여줌
    - Steps 내부는 여러 가지 스텝들로 구성되며 여러 작업들을 실행할 수 있음

### Declaratives

- 각 stage안에서 어떠한 일들을 할 것인지 정의하는 것
- 단계
    - Environment
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/875cbd4e-6a2b-4d2f-8e73-9a6385c95f23/Untitled.png)
    
    - Parameter
        - 파이프라인 실행 시 파라미터 받음
    - Triggers
        - 어떤 형태로 트리거 되는가?
        - 이 파이프라인이 어떤 주기로 실행이 되는가?
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b28dbbee-dada-47f1-becd-fcc64b3973b8/Untitled.png)
        
    - When
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f0425d5-b4df-4c96-bb3a-138e7522324f/Untitled.png)
    

# 오늘 느낀점

---

- 온라인 회의를 무사히 마쳤다. 월요일까지 텀이 짧아 많은 내용을 나누진 않았지만, 월요일은 오프라인 미팅이라 더 많이 진행할 수 있을 것 같다.
- 휴식도 중요하다고 들었다^~^ 오늘은 조금 쉬어갈 생각이다..ㅎㅎㅎㅎ

# 내일 목표

---

- CI/CD 자동화 구축 - 젠킨스
- JWT 토큰 복습