# 오늘 배운점

---

# 스프링 관련

## 새로 알게된부분

@Value값이 의존성 주입 시점에는 설정되지 않아서 필요시에는 @PreConstruct를 활용해야함.

- 스프링 빈의 이벤트 사이클
    - 스프링 컨테이너 생성
    - 빈 생성
    - 의존관계 주입
    - 초기화 콜백 : 빈이 생성되고 빈의 의존관계 주입이 완료된 후 호출됩니다.
    - 사용
    - 소멸전 콜백 : 빈이 소멸되기 직전에 호출됩니다.
    - 종료
- 스프링 빈 생명주기 콜백 사용법
    - InitializingBean, DisposableBean 사용
    - 설정 정보 초기화 메서드, 종료 메서드 지정
    - @PostConstruct, @PreDestroy 사용 (추천)
    

### @PostConstruct, @PreDestroy

- javax.annotaion
- 의존성 주입이 이뤄진 후 초기화를 실행하게 하는 어노테이션
- 컴포넌트 스캔과 잘어울림

### 예시

```java
@Service
public class BusinessServiceImpl implements BusinessService{
 
    @Autowired
    DataDAO dataDAO;
 
    private ParamDTO paramDTO;
 
    @PostConstruct
    public void initialize(){
        paramDTO = new ParamDTO();
    }
}
```

예시 출처 ) [https://zorba91.tistory.com/223](https://zorba91.tistory.com/223)

# CloudFront

- 정적, 동적 컨텐츠를 빠르게 응답하기 위한 CDN 서비스
- 캐싱을 지원하기 때문에 S3에 저장된 컨텐츠를 직접 접근하지 않아도 되므로 **S3의 비용이 감소하며, 더 빠른 응답을 지원**하므로 꼭 함께 적용해주는 것이 좋다.
    - S3 url을 통한 직접 접근이 아닌 CloudFront와 연동된 url로 요청 (캐싱돼있음)
- 애플리케이션과 인프라에 대한 “관문”으로 사용함으로써 중요한 콘텐츠, 데이터, 코드 및 인프라에 대한 주요 공격을 차단
- 콘텐츠, API 또는 애플리케이션을 SSL/TLS를 통해 전송 가능
- 특정 콘텐츠에 대한 액세스 제한 가능
- 가용성 향상 : 캐싱을 통한 Origin의 워크로드를 줄일 수 있음
- Amazon S3, Amazon EC2 또는 Elastic Load Balancing과 같은 AWS Origin을 사용하는 경우, 이러한 서비스와 CloudFront 간에 전송된 데이터에 대해서는 요금이 청구되지 않음

### CloudFront + S3 사용 시 고려사항.

- 사용자가 동일한 이름의 파일을 수정 업로드 했을때, 캐시에 남아있어서 반영되지 않은 이전 결과를 얻을 우려가 있음.
- 해결방법
    - 현재 날짜를 파일경로에 같이 기입하여 일치 여부를 통해 기존 파일을 삭제하거나 하는 방식을 활용.
    - 신규 업로드와 수정 업로드 동일 메서드 사용해도됨.

# 오늘 느낀점

---

- 대부분의 아키텍쳐 설계를 마친 것 같다! 내일 젠킨스 배포설정만 마무리하면 개발 단계로 넘어갈 수 있다 ㅎㅎㅎ 축구하는 날이니 오전 일찍 일어나서 공부 시작하기!

# 내일 목표

---

- 아키텍쳐 설계 진~짜 마무리!