# Chapter4. 주석

> 나쁜 코드에 주석을 달지마라. 새로짜라. (p.68)

잘 달린 주석은 그 어떤 정보보다 유용하다.

하지만 우리는 코드로 의도를 표현하지 못해, 실패를 만회하기 위해 주석을 사용한다. 

주석이 오래될수록 완전히 그릇된 정보일 가능성이 커진다.

프로그래머들이 주석을 유지하고 보수하기란 현실적으로 불가능하니까..

저자는 말한다..

> 코드를 깔끔하게 정리하고 표현력을 강화하는 방향으로, 그래서 애초에 주석이 필요없는 방향으로 에너지를 쏟겠다.

부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다 ! 

약간 만화대사같은 말.. 멋져

> 진실을 한곳에만 존재한다. 바로 코드다. (p.69)

코드만이 정확한 정보를 제공하는 유일한 출처다. *그러므로 우리는 (간혹 필요할지라도) 주석을 가능한 줄이도록 꾸준히 노력해야 한다.*

## 주석은 나쁜 코드를 보완하지 못한다

코드에 주석을 추가하는 일반적인 이유는 코드 품질이 나쁘기 때문이다.

표현력이 풍부하고 깔끔, 주석이 없는 코드 > 복잡하고 어수선하며 주석이 많이 달린 코드

## 코드로 의도를 표현하라!

```java
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다
if((employee.flags & HOURLY_FLAG) &&
    (employee.age > 65))
```

```java
if(employee.isEligibleForFullBenefits())
```

아래가 의도를 더 잘표현하고 있다! 주석 노필요.

## 좋은 주석

[Untitled](https://www.notion.so/7c15b6e075a644b89cec33385db4b7d9)

### 법적인 주석

ex) 각 소스 파일 첫머리에 주석으로 들어가는 저작권 정보, 소유권 정보

```
// Copyright (C) ...
// GNU General Public License ...
```

### 정보를 제공하는 주석

ex)

```
// 테스트 중인 Responder 인스턴스를 반환함
protected abstract ResponderInstance());
```

### 의도를 설명하는 주석

때때로 주석은 구현을 이해하도록 도와주는 것을 넘어서 의도까지 설명한다

```
 public int compareTo(Object o){
     if(o instanceof WikiPagePath){
         ....
     }
     // ~~한 경우에는 우선순위가 높다.
     return 1;
 }
```

```
public void testConcurrentAddWidgets() {
    ...
    // 스레드를 대량 생성하는 방법으로 경쟁 조건을 만든다.
    for (int i = 0; i < 25000; i++){
        new Thread(widgetBuilderThread).start();
    }
    ...
}
```

### 의미를 명료하게 밝히는 주석

때때로 모호한 인수나 반환값이 사용되었을 때가 있다.당연히 인수나 반환값 자체를 명확하게 바꾸는게 좋지만, 인수나 반환값을 변경하지 못한다면 의미를 명료하게 밝히는 주석이 유용하다.

```
...
 assertTrue(a.compareTo(a) == 0); // a == a
 assertTrue(a.compareTo(b) != 0); // a != b
...
```

### 결과를 경고하는 주석

요즘은 @Ignore 속성을 사용해 테스트 케이스를 꺼버린다.ex) `@Ignore("실행이 너무 오래 걸린다.")`

아래는 결과를 경고하는 주석의 예이다.ex)

```
// 여유 시간이 충분하지 않다면 실행하지 마십시오.
 public void _testWithReallyBigFile() {
     ...
 }
```

```
public static SimpleDateFormat makeStanardHttpDateFormat() {
     // SimpleDateFormat은 스레드에 안전하지 못하다.
     // 따라서 각 인스턴스를 독립적으로 생성해야 한다.
     SimpleDateFormat df = new SimpleDateFormat(".......");
     ...
     return df;
 }
```

### TODO 주석

앞으로 해야 할일을 주석으로 남김

```
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception
{
    return null;
}
```

### 중요성을 강조하는 주석

뭔가의 중요성을 강조하려고 주석을 사용하기도 한다.

```
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문저열로 인식되기 때문이다.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

### 공개 API에서 Javadocs

공개 API 구현 시 Javadocs를 잘 작성하자. (Javadocs 역시 다른 주석과 마찬가지로 잘못된 정보를 전달할 가능성이 있음)

## 나쁜 주석

대다수의 주석은 나쁜 주석에 속한다.

- 허술한 코드를 지탱
- 엉성한 코드를 변명
- 미숙한 결정을 합리화

## 결론

애초에 주석이 없는 방향으로 에너지를 쏟고, 주석은 간단 명료하게 코드를 더 읽기 좋게해주는 첨가제 느낌으로 사용되어야 한다. 코드만 봤을때 주석이 필요없을 정도면 더할나위 없이 좋다.

개인적으로 주석이 아예 없는 코드 보다는 주석이 적당하게 달린 코드들이 읽기 편한것같다. 주석을 적절히 달되, 신경써서 달아준 코드를 읽을때 훨씬 가독성있게 읽힌다. 주석