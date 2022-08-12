# 오늘 배운점

---

### 설정파일 분리

- profile 설정을 통해 yml 파일을 개발 환경에 맞추어 분리하여 작성할 수 있다.
- default 설정

```yaml
spring:
  profiles:
    default: dev # 기본 환경을 dev로
```

- 나머지는 아래처럼

```yaml
spring:
  config:
    activate:
      on-profile: create # 환경이름설정

```

- 실행 시점에서 active값을 원하는 환경으로 넘기면 됨
    - **java -jar myApp.jar --spring.profiles.active=dev**
- 인텔리제이로 실행시 Edit Configurations 에서 Active profiles 수정해서 실행하면됨

### AWS Parameter Store

- yml 작성 예시

```yaml
spring:
  config:
    activate:
      on-profile: production
    import: 'aws-parameterstore:'
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: ${jdbc.url}
    username: ${jdbc.username}
    password: ${jdbc.password}
aws:
  paramstore:
    enabled: true
    prefix: /config
    profile-separator: _
    name: {name}
```

- AWS Parameter Store 파라미터 규칙
    - **`{prefix}/{name}{profile-separator}{profile}/key`**
- prefix
    - 파라미터의 접두사를 지정할 수 있다. 주의해야할 점은 해당값은 /로 시작해야한다.
    - default : /config
- name
    - 파라미터의 식별자 애플리케이션이름이다. 해당 파라미터를 어떤 애플리케이션에 적용할건지를 지정할 수 있다.
    - 만일 해당값을 지정하지 않으면 spring.application.name 속성에 정의된 값을 참조하게 된다.
    - 해당 속성마저도 없으면 'application' 이라는 이름이 부여된다.
    - default : application
- profile-separator
    - 하나의 애플리케이션을 여러환경에 배포할 수 있게끔 구분자를 지정해두는데 이 속성은 위 name과 같이 쓰인다.
    - 데이터베이스 엔드포인트 값을 예로들면 /config/{name}_local/jdbc.url, /config/{name}_production/jdbc.url 이렇게 구성된다.
    - default : _
- profile
    - spring.config.activate.on-profile에 정의된 값이다.
- enabled
    - AwsParamStoreBootstrapConfiguration를 활성화 한다.
    - default : true
    

참고블로그 : [https://kim-jong-hyun.tistory.com/120](https://kim-jong-hyun.tistory.com/120)

# 오늘 느낀점

---

- 팀 프로젝트의 방향이 조금 바뀌었다. 다음 주 까지 MVP 모델 배포를 하기 위해 적당한 완성 규모를 설정했다고 생각했는데, 오늘 회의 중 프론트 팀원이 시간이 많이 부족할 것 같다는 의견을 냈다.
개발 속도를 맞추기 위해서라도 팀원간의 소통이 중요하다는 것을 배울 수 있었다.

# 내일 목표

---

- 팀 멘토링