# 오늘 배운점

---

## 배포 환경 세팅관련 공부

- ECR (Elastic Containor Registry)
    - 도커 이미지를 저장하는 프라이빗 레포지토리
    - 실제 프로덕션 환경에서는 containor 기반의 배포를 할 것이기 때문에 반드시 repository가 있어야함
- ECS (Elastic Containor Service)
    - 도커 컨테이너 기반으로 서비스 운용을 가능하게 해주는 간단한 서비스
    - 무중단 배포(롤링방식)를 제공함
    - 백엔드 서비스를 scale up 가능한 형태로 배포하는데 최적화
    - 수많은 도커 컨테이너 서버를 띄우고 로드밸런싱 해줌
    - fargate, EC2 모드가 있어서 도커 컨테이너 리소스만 띄우거나 혹은 물리적인 EC2 인스턴스 클러스터로 구성 가능
    - ECS 혹은 쿠버네티스로 롤링 배포가 가능하기 때문에 결론적으로 젠킨스는 배포 명령만 내려주면 된다.
        - ex ) aws ecs update-service 서비스명
- 비용이 관련 없다면 안전하게 클라우드 리소스를 dev / prod 로 계정을 분리해서 하기도 함

### Jenkinsfile

```
pipeline {
    //agent 지정
    agent any

    //트리거 설정 (예시는 3초 주기로 레포 확인)
    triggers {
        pollSCM('*/3 * * * *')
    }

    //환경변수 세팅 (각 리소스 활용하기 위한 계정정보)
    environment {
        AWS_ACCESS_KEY_ID = credentials('awsAccessKeyId')
        AWS_SECRET_ACCESS_KEY = credentials('awsSecretAccessKey')
        AWS_DEFAULT_REGION = 'ap-northeast-2'
        HOME = "."
    }
    
}
```

- environment
    - 젠킨스용 AWS User를 만들어서 AWS 명령어를 사용할 수 있는 권한 부여 및 키 생성 후 Jenkinsfile에 액세스 키 입력
    - 발급받은 액세스 키로 젠킨스에서도 ‘awsAccessKeyId’, ‘awsSecretAccessKey’의 이름으로 credential 추가

[강의 출처] [https://www.youtube.com/watch?v=JPDKLgX5bRg](https://www.youtube.com/watch?v=JPDKLgX5bRg) (토크 ON 세미나 젠킨스편)

# 오늘 느낀점

---

- 젠킨스 설정을 너무 구글보고 베꼈더니 배포환경 세팅하는데 문제가 많이 발생하고, 각각의 과정에 대한 이해도가 부족하다고 생각하여 추가적으로 공부를 해보았다. 생각보다 양이 많아서 내일까지 이어서 할 생각이다!

# 내일 목표

---

- 팀 회의
- 배포환경 세팅
- 축구^~^