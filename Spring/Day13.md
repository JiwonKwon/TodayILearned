# day13

```java
org.springframework.http.converter.HttpMessageConversionException: Type definition error: [simple type, class com.jidong.ccadui.controller.api.dto.KakaoLoginResultV1]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.jidong.ccadui.controller.api.dto.KakaoLoginResultV1` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
 at [Source: (PushbackInputStream); line: 1, column: 2]
```

기본생성자가 없어서 나는 오류 

유독 ControllerTest 실행할 때 많이 발생한다.

```java
@Data
@NoArgsConstructor // 추가해줬음
public class KakaoLoginResultV1 {
    @ApiModelProperty("결과 코드")
    private String resultCode;

    @ApiModelProperty("결과 메세지")
    private String resultMessage;

    @ApiModelProperty("jwt Token")
    private String jwtToken;
}
```

@NoArgsConstructor 어노테이션으로 기본생성자를 추가해줘서 해결함.