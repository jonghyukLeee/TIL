# 오늘 배운점

---

# AWS 심화교육(**Architecting on AWS) - 1일차**

AZ(가용영역) → 데이터센터의 집합

리전(region) → AZ의 집합

엣지 로케이션

- 클라이언트에게 짧은 지연시간을 제공하기 위해 사용
- 대표적인 서비스로는 CloudFront , Route 53
- 캐싱전략을 통해 빠르게 제공할 수 있음

가장 일반적인 아키텍쳐

![33](https://user-images.githubusercontent.com/79312551/180034490-15e8dd1a-9feb-4087-b723-f205e0dc13fc.png)

- s3, dynamoDB등은 리전 내부에 만듦. (vpc랑은 연관없음)
- 가용영역(az)는 두개정도 (비용때문)
- 대부분의 서비스는 리전 범위내에 생성하고, ec2, ebs 등은 vpc 내에 만듦

## IAM

- Account
    - account 하나 생성할때마다 숫자 12개의 account ID 생성됨.(식별자)
- AWS는 Root user는 사용하지 않도록 권장함.
    - 최대 권한을 지니고 있지만 권한 제어는 불가능함.
    - 모든 사람이 동일한 계정을 사용하게 되기 때문에 서로 누군지 알 수 없다.
- IAM의 Entity
    - user
        - 영구 자격증명
        - 인증방식
            - username / pwd (console)
            - Access Key ID / Secret Access Key (CLI,SDK,API)
    - group
    - policy
        - 권한은 JSON 형식
        - user,group,role에 부여할 수 있음
        - 정책 적용 방식
            - Identity (유저에 권한을줌)
            - Resource (리소스 자체에 접근 권한을 둠)
    - role
        - 임시 자격증명(만료기간이 있음)
        - 권한위임 (가능범위)
            - IAM User
            - Federation User(외부 사용자)
            - AWS 서비스
        - 최소 단위로 권한을 부여하기 위해 위임방식을 사용함
- User vs Role
    - 보안상의 문제 때문에 임시 자격증명인 Role을 권장함.
    

### Organizations

- 통합결제 , 모든기능
- 계정을 관리해주는 서비스
- 트리구조
- Entity
    - Organization
    - Account
        - management
        - member
    - OU
    - Policy
        - SCP
        - Tag Policy

### IAM과 Organizations를 구분하여 학습해보기

### 정책 유형

- 최대 권한 설정
    - IAM 권한 경계(범위설정)
    - AWS Organizations 서비스 제어 정책(SCP)
- 권한 부여
    - IAM 자격증명 기반 정책
    - IAM 리소스 기반 정책

# 오늘 느낀점

---

### AWS 강의 후기

- 강의 내용도 알차고, 평소에 궁금했던 부분이었어서 유익한 시간이었던 것 같다. 근데 9시 30분부터 5시 30분까지 진행을 하니까, 하루종일 강의만 듣는 것이 너무 지루했고 집중력도 점점 흐려져서 효율이 떨어졌다 ㅠ
내일은 공부 환경을 바꿔서 오늘보다 더 집중해볼 생각이다!

# 내일 목표

---

- AWS 심화교육
