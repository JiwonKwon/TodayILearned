# Chapter9. 단위 테스트

우리 분야에 테스트 자동화는 점점 더 늘어나는 추세

이 장에서는 제대로된 테스트 케이스를 작성해야한다는 좀 더 미묘한 (그리고 더 중요한) 사실을 소개합니다.

---

## TDD 법칙 세가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

이 규칙은 빙산의 일각에 불과하다. 

위 세가지 규칙을 따르면 개발과 테스트가 대략 30초 주기로 묶인다.

테스트 코드와 실제코드가 함께 나오고, 테스트 코드가 실제 코드보다 불과 몇 초 전에 나온다.

But 이렇게 일하면 실제 코드를 사실상 전부 테스트하는 코드가 나오며, 실제 코드와 맞먹을 정도로 방대한 테스트 코드는 심각한 관리문제를 유발하기도 한다.

---

## 깨끗한 테스트 코드 유지하기

저자는 테스트코드를 '지저분해도 빨리' 만드는 방식으로 개발했던 경험을 소개한다. 

→ 그런데 테스트코드는 지저분하면 할수록 변경하기가 어려워진다.

→ 이 경우, 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸린다.

→ 새 버전을 출시할때마다 팀이 테스트 케이스를 유지하고 보수하는 비용도 늘어난다.

→ 결국 개발자들은 불만을 갖고,, 테스트 코드를 비난하며 폐기하게되는 상황까지 이른다.

테스트 코드를 깨끗하게 짯다면 테스트에 쏟아부은 노력은 허사로 돌아가지 않았을 것.

> 테스트 코드는 실제 코드 못지 않게 중요하다. (p.167)

---

## 테스트는 유연성, 유지보수성, 재사용성을 제공한다

테스트 코드를 깨끗하게 유지하지 않으면 결국은 잃어버린다. 그리고 테스트 케이스가 없으면 실제코드를 유연하게 만드는 버팀목도 없어진다.

> 단위테스트 = 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목

이유는 단순하다. 테스트 케이스가 있으면 변경이 두렵지 않으니까!

---

## 깨끗한 테스트 코드

깨끗한 테스트 코드를 만들려면? 세 가지가 필요하다. **가독성, 가독성, 가독성.**

어쩌면 가독성은 실제 코드보다 테스트 코드에 더더욱 중요하다.

목록 9-1은 자질구레한 사항이 너무 많아 테스트 코드의 표현력이 떨어진다.

목록 9-1 SerializedPageResponderTest.java

```java
public void testGetPageHieratchyAsXml() throws Exception {
  crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
}

public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
  WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
  crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
  crawler.addPage(root, PathParser.parse("PageTwo"));

  PageData data = pageOne.getData();
  WikiPageProperties properties = data.getProperties();
  WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
  symLinks.set("SymPage", "PageTwo");
  pageOne.commit(data);

  request.setResource("root");
  request.addInput("type", "pages");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

  assertEquals("text/xml", response.getContentType());
  assertSubString("<name>PageOne</name>", xml);
  assertSubString("<name>PageTwo</name>", xml);
  assertSubString("<name>ChildOne</name>", xml);
  assertNotSubString("SymPage", xml);
}

public void testGetDataAsHtml() throws Exception {
  crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");

  request.setResource("TestPageOne"); request.addInput("type", "data");
  Responder responder = new SerializedPageResponder();
  SimpleResponse response =
    (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
  String xml = response.getContent();

~~~~  assertEquals("text/xml", response.getContentType());
  assertSubString("test page", xml);
  assertSubString("<Test", xml);
}
```

위 코드는 읽는 사람을 고려하지 않는다.

목록 9-2 SerializedPageResponderTest.java (refactored)

```java
public void testGetPageHierarchyAsXml() throws Exception {
  makePages("PageOne", "PageOne.ChildOne", "PageTwo");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}

public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
  WikiPage page = makePage("PageOne");
  makePages("PageOne.ChildOne", "PageTwo");

  addLinkTo(page, "PageTwo", "SymPage");

  submitRequest("root", "type:pages");

  assertResponseIsXML();
  assertResponseContains(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
  assertResponseDoesNotContain("SymPage");
}

public void testGetDataAsXml() throws Exception {
  makePageWithContent("TestPageOne", "test page");

  submitRequest("TestPageOne", "type:data");

  assertResponseIsXML();
  assertResponseContains("test page", "<Test");
}
```

`BUILD-OPERATE-CHECK` 패턴이 위와같은 코드. 코드가 수행하는 기능을 재빨리 파악할 수 있다.

**BUILD-OPERATE_CHECK패턴?**

- BUILD: 테스트 자료를 만든다
- OPERATE: 테스트 자료를 조작한다
- CHECK: 조작한 결과가 올바른지 확인한다

![Chapter9%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%8B%E1%85%B1%20%E1%84%90%E1%85%A6%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%2002355a555e6f4350a2dd242809083622/Untitled.png](Chapter9%20%E1%84%83%E1%85%A1%E1%86%AB%E1%84%8B%E1%85%B1%20%E1%84%90%E1%85%A6%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%2002355a555e6f4350a2dd242809083622/Untitled.png)

---

## 도메인에 특화된 테스트 언어 (DSL : Domain Specific Language)

위의 `목록 9-2`는 도메인에 특화된 언어로 테스트 코드를 구현하는 기법을 보여준다.

시스템 조작 API를 사용하는 대신 위에다 함수와 유틸리티를 구현한 후 그 함수와 유틸리티를 사용

→ 테스트 코드를 짜기도 읽기도 쉬워진다. 

 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩토링해야 마땅하다.

---

## 이중 표준

→ 실제 환경에서는 절대 안되지만 테스트 환경에서는 전혀 문제없는 방식이 있다. 실제 환경에서는 문제가 될 수 있지만 테스트 환경은 자원이 제한적일 가능성이 낮기 때문이다.

실제 환경 : 컴퓨터 메모리 CPU 효율을 중시해야함. String 보다 StringBuffer를 쓰면 효율성 좋음

테스트 환경 : 효율? 노상관. String 걍써

코드의 깨끗함과는 철저히 무관하며 대개 메모리나 CPU 효율과 관련있는 경우이다.

---

## 테스트 당 assert 하나

JUnit으로 테스트 코드를 짤 때 함수마다 assert를 단 하나만 사용해야 한다고 주장하는 학파가 있다. 

가혹하다 여길지 모르지만 확실히 장점이 있다.

> assert가 하나라면 결론이 하나기 때문에 코드를 이해하기 빠르고 쉽다.

단일 assert문을 만들면서 중복이 생기는 경우가 있음

→ 중복을 제거하기 위해 TEMPLATE METHOD 패턴 사용

- given/when을 부모 클래스에 두고, then을 자식 클래스에 둠
- 독자적인 클래스를 만들어 @Before 함수에 given/when, @Test 함수에 then

→ 배보다 배꼽이 커지는 상황 

결론) 

`단일 assert문`은 휼륭한 지침이나 때로는 함수 하나에 여러 assert문을 넣는 것이 나을 수도 있다. assert문은 최대한 줄이는 게 좋다

---

## 테스트 당 개념 하나

테스트 함수 하나는 한 개념만 테스트하라 

---

## F.I.R.S.T

다섯가지 규칙 

**Fast**(빠르게): 테스트는 빨라야 한다.

 - 테스트가 느리면 자주 못돌림 → 못돌리면 문제 못찾아지고 코드품질이 낮아짐.

**Independent**(독립적으로): 각 테스트는 서로 의존하면 안된다.

 - 독립적으로 어떤 순서로 실행해도 괜찮아야 한다.

**Repeatable**(반복가능하게): 테스트는 어떤 환경에서도 반복 가능해야 한다.

 - 실제 환경, QA 환경, 네트워크 없는 환경에서도 실행될 수 있어야한다.

**Self-Validating**(자가검증하는): 테스트는 부울(bool)값으로 결과를 내야 한다.

 - 성공아니면 실패다.

**Timely**(적시에): 테스트는 적시에 작성해야 한다.

 - 실제 코드 구현하기 직전에 구현한다.

---

## 결론

테스트코드는 프로젝트 건강에 중요하며, 실제 코드보다 더 중요할 수 도 있다.

테스트코드는 지속적으로 깨끗하게 관리하자! 

테스트 언어를 구현해 도메인 특화언어 (DSL)를 만들자.