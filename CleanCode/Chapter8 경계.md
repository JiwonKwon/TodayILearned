# Chapter8. 경계

시스템에 들어가는 소프트웨어를 직접 다 개발하는 경우는 드물다 

- 구매한 패키지, 오픈소스 등을 사용
- 사내 다른 팀이 제공하는 컴포넌트를 사용

이런 경우에 소프트웨어 경계를 깔끔하게 처리하는 기법과 기교를 설명하는 장이다.!

---

## 외부 코드 사용하기

인터페이스 제공자 (패키지 제공자 & 프레임워크 제공자) : 적용성을 최대한 넓히려고  애씀. 더 많은 환경에 돌아가야하므로..! 

인터페이스 사용자 : 자신의 요구에 집중하는 인터페이스를 바란다.

이런 긴장으로 인해 시스템 경계에서 문제가 생길 소지가 많다.

### *예시 - java.util.Map*

- clear() void - map
- containsKey(Object key) boolean - Map
- containsValue(Object value) boolean - Map
- entrySet() set - Map
- equals(Object o) boolean - Map
- get(Object key)Object - Map
- getClass() Class<? extends Object> - Object
- hashCode() int - Map
- isEmpty() boolean - Map
- keySet() Set - Map
- notify() void - Object
- notifyAll() void - Object
- put(Object key, Object value) Object - Map
- putAll(Map t) void - Map
- remove(Object key) Object - Map
- size() int - Map
- toString() String - Object
- values() Collection - Map
- wait() void - Object
- wait(long timeout) void - Object
- wait(long timeout, int nanos) void - Object

Map은 굉장히 많은 인터페이스로 수많은 기능을 제공한다. 

편리하지만,, 그만큼 위험도 클 수 있다.

 

예시 1)  누구나 clear() 할 권한이 있다는 것 

예시 2) 설계시에 Map 에 특정 객체 유형만 저장하기로 결정했어도, Map은 객체 유형을 제한하지 않음. 마음만 먹으면 사용자는 어떠한 객체 유형도 추가할 수 있다.

### 특정 객체를 담으려고 Map을  사용한 경우

Sensor라는 객체를 담는 Map을 만드려면 다음과 같이 Map 을 생성한다

```java
Map sensors = new HashMap();
```

Sensor 객체가 필요한 코드는 다음과 같이 Sensor 객체를 가져온다.

```java
Sensor s = (Sensor)sensors.get(sensorId);
```

위와 같은 코드가 한번이 아니라 여러차례 나옴. 

즉, Map이 반환하는 객체를 올바른 유형으로 변환할 책임은 Map을 사용하는 클라이언트에 있다. 

물론 코드는 동작하지만 깨끗한 코드라 보기는 어렵다. 게다가 의도도 분명하게 드러나지 않는다.

## 제네릭을 사용한 Map

```java
Map<String, Sensor> sensors = new HashMap<Sensor>();
...
Sensor s = sensor.get(sensorId);
```

코드 가독성이 높아진다. 

하지만 `Map<String, Sensor>`이 **사용자에게 필요하지 않은 기능까지 제공**한다.는 **문제**는 해결하지 못한다.

프로그램에서 Map<String, Sensor> 인스턴스를 여기저기로 넘긴다면, Map 인터페이스가 변할 경우, 수정할 코드가 상당히 많아진다. 

### 캡슐화를 사용한 Map (일반 Map, 제네릭의 문제 해결)

위의 문제들은 객체를 감싸는 방법으로 해결이 가능합니다.

- Sensors 사용자는 제네릭스가 사용되었는지 어쨋는지 신경쓰지 않아도 됨
    - 제네릭스의 사용여부는 Sensor안에서 결정한다.
- 필요하지 않는 인터페이스(clear()와 같은)를 제공하지 않을 수 있음
    - Sensor 클래스는 프로그램에 필요한 인터페이스만 제공하기 때문에 코드는 이해하기 쉬우며, 오용하기 어렵다.

```java
public class Sensors {
	private Map sensors = new Hashmap();

	public Sensor getById(string id) {
		return (sensor) sensors.get(id);
	}
}
```

### Map을 사용하는 방식에 대한 결론

Map을 사용할때마다 위와같이 캡슐화를 하라는 것은 아니다. Map(혹은 유사한 경계 인터페이스를) 여기저기 넘기지 말라는 것이다. 

Map과 같은 경계 인터페이스를 사용할때는 클래스 밖으로 노출되지 않도록 주의한다.

Map 인스턴스를 공개 API 의 인수로 넘기거나 반환값으로 사용하지 않는다.

---

## 경계 살피고 익히기

> 외부 코드를 익히기는 어렵다. 외부 코드를 통합하기도 어렵다. 두 가지를 동시에 하기는 두 배나 어렵다.

`학습 테스트` : 곧바로 외부코드와 기존 코드를 통합하기전에, **먼저 간단한 테스트 케이스를 작성해 외부 코드를 익히는 것**

- 학습 테스트는 프로그램에서 사용하려는 방식대로 외부 API를 호출한다.
- 통제된 환경에서 API를 제대로 이해하는지를 확인하는 셈이다.
- 학습테스트는 API를 사용하려는 목적에 초점을 맞춘다.

---

## log4j 익히기

이 부분에서는 아파치의 log4j 패키지를 사용을 가정하며, 경계 인터페이스를 구분하는 과정을 설명한다. 직접 돌아가는 방식을 이해하며, 경계를 구분하고 독자적인 로거클래스로 캡슐화하고 나면 나머지 프로그램은 log4j 경계 인터페이스를 몰라도 된다.

---

## 학습 테스트는 공짜 이상이다

학습테스트에 드는 비용은 없다. 어쨋든 API를 배워야하므로..! 

패키지의 새버전이 나오면 학습  테스트를 돌려 차이가 있는지 확인한다. 이러한 과정의 경계 테스트는 패키지의 새 버전으로의 이전을 쉽게 만든다.

---

## 아직 준비하지 않는 코드를 사용하기

이 부분에서는 아직 타팀에서 설계하지 않은 API 를 자체적으로 정의하여 개발했던 내용을 설명한다. 

- 필요한 인터페이스를 구현하면 우리가  인터페이스를 전적으로 통제할 수 있다는 장점
- 코드의 가독성도 높아지고 코드 의도도 분명해짐

---

## 깨끗한 경계

소프트웨어 설계가 우수하다면 볍경하는데 많은 투자와 재작업이 필요하지 않다.

항상 깨끗한 경계를 유지하자! 

- 통제 불가능한 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 좋다!
- 외부 패키피를 호출하는 코드를 가능한 줄여 경계를 관리
    - 새로운 클래스로 경계를 감싸거나,
    - Adapter 패턴을 사용 : 우리가 원하는 인터페이스를 패기지가 제공하는 인터페이스로 변환