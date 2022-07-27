# 오늘 배운점

---

# 자동화

## 코드형 인프라(IaC)

- CloudFormation
    - JSON 또는 YAML 형식의 템플릿
    - 하나로 여러개의 스택을 만들 수 있음 (재사용 가능)
    - 순차적으로 생성되지 않기 때문에 VPC보다 EC2가 먼저 생성되는 경우가 생길수도 있다.
    따라서, 템플릿을 계층화 해야한다.
    
# MSA

- AWS 서비스로서의 배포 서버 단위
    - VM
        - EC2
    - Containor
        - ECS
        - EKS
    - Serverless
        - Lambda

### Lambda

- 직접호출
    - 호출할때 실행환경이 구성됨.
    - 여러명이 동시에 하나의 함수를 요청하더라도 각각의 환경에서 따로 실행됨.
- 예약
- 이벤트
- 함수 자체에 접근 ROLE을 지정해줘야함 (Execution Role)

## Messaging Service

- SQS (Simple Queue Service)
    - 완전 관리형 메시지 대기열 서비스
        - 표준(standard) : 거의 제한 없는 초당 API 호출
        - FIFO : 제한된 초당 API 호출
    - 오토스케일링 연계 가능
    - 폴링 방식의 메시지큐
    - 1 : 1 관계
- SNS (Simple Notification Service)
    - 토픽을 구독하는 방식
    - 1 : N 관계
    - 서울리전은 SMS 불가능, 이메일은 됨
    

### Amazon Kinesis

- Data Streams
- Data Firehose
- Data Analytics

# 오늘 느낀점

---

- 강의는 짧고 굵을수록 좋은 것 같다..ㅎㅎ 너무 빡센 강의 시간 탓에 중간중간 조금 지루했지만, 필요한 부분은 확실하게 습득할 수 있었던 것 같다. 3일간 정말 유익한 시간이었다!

# 내일 목표

---

- 팀회의