# day2

## 오늘은 정말루 끄적끄적

스프링프레임워크에선 Bean 을 주입받는 방식

- `@Autowired`
- setter
- 생성자

Autowired는 비권장방식

그러므로 생성자로 Bean 객체를 받도록하면 Autowired와 동일한 효과를 볼 수 있음.

`@AllArgsConstructor` 로 생성자 만들기 쌉가넝

`@ActiveProfiles(profiles = "h2")`

application-h2.yml 설정을 보는것

`@Profile`과 `@ActiveProfiles` 둘 다 application.properties 파일들에 따라서 Spring Bean을 만드는데 (개발환경과 배포환경을 구분지어줄 수 있다는 뜻) @Profile은 SpringBootApplication을 진짜 실행할 때 사용하는거고 @ActiveProfiles는 테스트를 할 때 사용한다.

## 오늘의 문제 (해결안됨)

오늘은 멀티DB환경에서 로컬테스트를 하는데 다 테스트가 실패했다. (원래부터 바뀌고 계속 그랬다구함...왜아무도 안고치죠...?)

datasource가 추가되면서 뭐가 꼬인것같은데..^^

두개의 db가 있을때 h2로 데이터 insert 구분을 할 수가 없어서 그런가? 모르겠따 모르겠어 !!!!!

왜 안되는건지는 내년에 팀원분한테 물어보겠따. 벌써 다음주가 내년..

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'mpEntityManagerFactory' defined in class path resource [vine/mb/common/config/MPDBConfig.class]: Initialization of bean failed; nested exception is org.springframework.jdbc.datasource.init.ScriptStatementFailedException: Failed to execute SQL script statement #1 of class path resource [data-main.sql]: Insert into MB_ADDR

아 넘 춥다 오늘은 걷기운동도 7000보밖에 못했다 ㅠㅠ 넘춥지만 낼도 열공하쟈~.~
