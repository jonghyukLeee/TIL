# 오늘 배운점

---

## Redis 서버 구축시 고려사항

- 데이터 관리
    - 캐싱 전략
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd4e6c24-7a8d-4c1d-b90c-fecd7e7e3367/Untitled.png)
    
    - 백업여부
        - 메모리에 저장하면 입출력 성능은 증가하지만, 영속성은 보장되지 않음
        - 이러한 데이터 휘발성을 보완하기 위해 Redis에서는 RDB(Redis Database)와 
        AOF(Append Only File) 라는 백업 옵션을 지원.
        - 데이터 중요도가 낮으면 굳이 고려하지 않아도 됨.
- 스탠드얼론, 센티널, 클러스터
    - Redis의 세 가지 모드
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7fc092f4-6fda-48f7-bc39-2ebe8bf69c33/Untitled.png)
    
    - 스탠드 얼론
        - 마스터 1대만 운영하기 때문에 해당 서비스가 기능하는데 필요한 데이터가 많지 않고, 서버 장애 시 서비스에 대한 영향이 크지 않은 경우 선택.
    - 센티널
        - 마스터, 슬레이브와 함께 센티널 노드를 설치한다.
        - 마스터 노드를 감시하고 있다가 장애가 발생하면 슬레이브를 마스터로 승격시켜 가용성을 보장
    - 클러스터
        - 센티널 모드에서 더 나아가 데이터 규모가 커질 경우 샤딩까지 지원
        - 사실상 센티널 모드의 업그레이드 버전이기 때문에 센티널은 잘 안씀
- 관리형 vs 설치형
    - 대표적 호스팅 방식 장단점 비교
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/472bdbad-bcf3-4ce2-b9f6-e55f6c56612e/Untitled.png)
    

# 오늘 느낀점

---

- 요즘 날씨가 많이 풀리고 있는 것 같다 ㅎㅎ 얼른 끝나라 여름…..

# 내일 목표

---

- 스프링 스터디
- redis 서버