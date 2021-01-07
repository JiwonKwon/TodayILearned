# day9

## IntelliJ에서 Junit Test 실시간 반영이 안되는 현상

### 계속되는 테스트 실패

```bash
java.lang.NullPointerException
	at com.jidong.ccadui.domain.member.service.MemberServiceTest.insert_Member_Test(MemberServiceTest.java:50)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.platform.commons.util.ReflectionUtils.invokeMethod(ReflectionUtils.java:686)
```

Test가 안된다..

반영도 안된다.. 

우울해졌다.. 

주석처리를 했다,, 여전히 예전과 똑같은 오류 말도안되는 일이다ㅠ 실시간 반영안되는줄 알구 계속 구글링했는데 . . .  .

[https://stackoverflow.com/questions/52087055/unable-to-run-junit-5-test-in-intellij-2018-2-2-w-gradle-4-9](https://stackoverflow.com/questions/52087055/unable-to-run-junit-5-test-in-intellij-2018-2-2-w-gradle-4-9)

Preferences → Build, Execution, Deployment → Gradle → Runner → Gradle Test Runner 로 해결

진짜 허망하고 허망하고 허망하다 

자꾸 예전 결과 나왔던것보면 뭔가 clean이 안되었었다보다

`/.gradlew clean test` 가 제일 확실한걸루 ~~