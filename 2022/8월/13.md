# 오늘 배운점

---

### orElse() vs orElseGet()

- orElse()
    - 메소드를 인수로 넣게되면 null 여부에 상관 없이 메소드가 실행됨.
- orElseGet()
    - null일때만 메소드가 실행됨
- 두 경우를 잘 구분하여
null일때 메소드를 실행해야한다면 orElseGet(),
단순히 값을 넘기는 경우라면 orElse() 를 활용한다.

### AttributeConverter

- JPA가 지원하지 않는 타입을 매핑할 때 사용하는 인터페이스
- Converter 정의

```java
@Converter
public class StringListConverter implements AttributeConverter<List<?>, String> {

    private static final ObjectMappermapper= new ObjectMapper();
    //List to JSON
    @Override
    public String convertToDatabaseColumn(List attribute) {
        try {
            returnmapper.writeValueAsString(attribute);
        } catch (JsonProcessingException e) {
            throw new IllegalArgumentException();
        }
    }

    //JSON to List
    @Override
    public List<String> convertToEntityAttribute(String dbData) {
        try {
            returnmapper.readValue(dbData, List.class);
        } catch (IOException e) {
            throw new IllegalArgumentException();
        }
    }
}
```

- Entity 클래스에 적용

```java
@Convert(converter = StringListConverter.class)
@Column(columnDefinition = "json")
private List<String> urlList;
```

# 오늘 느낀점

---

- 흐리고 선선한 날씨에 취해 게으른 오전을 보냈다….. 내일은 정신 바짝차리고 빡공!

# 내일 목표

---

- WebClient
- 저녁약속!