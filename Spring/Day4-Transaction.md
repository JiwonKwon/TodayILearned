오늘은 Transcational과 Rollback 시 Exception 처리에 대한 고뇌를 하게 되었다.

![image](https://user-images.githubusercontent.com/44457591/103440332-e7e87800-4c87-11eb-98b4-d60d69af5ffa.png)

개발 하다보면 여러가지 상황에서 트랜잭션 단위의 비즈니스 로직을 구현하게 된다.

이때 트랜잭션을 잘 묶어줘야 로직이 꼬이지 않게된다. (가장 흔하디흔해서 질리는 예로는 계좌이체가 있다)

---

### _Transaction 이란, _

트랜잭션(Transaction)은 데이터베이스의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산들을 의미한다.

![image](https://user-images.githubusercontent.com/44457591/103440338-f59dfd80-4c87-11eb-958f-7284f357bff2.png)

(너무 흔하지만) 계좌이체로 예를 들면

1) 송금을 위해 계좌와 금액을 입력한다.

2) 송금자에게 돈을 보낸다

3) 송금자가 돈을 받는다.

이 세가지 단계 중 하나라도 진행되지 않으면 계좌이체가 이루어 질 수 없다.

가령 송금자에게 돈이 빠져나갔는데 송금자가 돈을 받지 못하는 상황이 생길 수 있기때문에 ...!

이러한 단계를 묶는것을 트랜잭션이라 한다. 

트랜잭션은 ACID원칙이라구  원자성(Atomicity), 일관성(Consistency), 고립성(Isolation), 지속성(Durability)이

만족되어야 하는 그런것이 있는데, 그냥 알아만 두면 될 것 같다.

DASP 시험 준비할 때 공부했었던 듯하다,,

---

### _Exception 상황에서 Transaction 처리_

그렇다면 Exception 상황에선 트랜잭션이 어떻게 처리될까용

궁금하져?

안궁금해도 궁금합시다 ~~~ (나에게 하는말)

Exception이란? 프로그램의 흐름을 방해하는 예외적 이벤트.

Exception의 종류는 Checked Exception과 Unchecked Exception 이 있다. 

<table style="border-collapse: collapse; width: 100%; height: 57px;" border="1" data-ke-style="style12"><tbody><tr style="height: 19px;"><td style="width: 33.3333%; height: 19px;">Exception 종류</td><td style="width: 33.3333%; height: 19px;">뜻</td><td style="width: 16.6667%; height: 19px;">예</td><td style="width: 16.6667%; height: 19px;">스프링 Transaction에서 롤백 여부</td></tr><tr style="height: 19px;"><td style="width: 33.3333%; height: 19px;"><span style="color: #333333;">Checked Exception</span></td><td style="width: 33.3333%; height: 19px;"><span>Exception의 상속받는 하위 클래스 중 Runtime Exception을 제외한 모든 Exception<br><br></span>명시적 예외처리가 필수</td><td style="width: 16.6667%; height: 19px;"><span>FileNotFoundException<br><span>ClassNotFoundException</span><br><span>DataFormatException<br>...</span></span></td><td style="width: 16.6667%; height: 19px;"><b><span>X<br></span></b></td></tr><tr style="height: 19px;"><td style="width: 33.3333%; height: 19px;"><span style="color: #333333; font-family: AppleSDGothicNeo-Regular, 'Malgun Gothic', '맑은 고딕', dotum, 돋움, sans-serif;">Unchecked Exception</span></td><td style="width: 33.3333%; height: 19px;"><span>Runtime Exception 하위 Exception</span></td><td style="width: 16.6667%; height: 19px;"><span style="color: #333333;">IndexOutOfBoundsException<br><span style="color: #333333;">NullPointerException</span><br><span style="color: #333333;">ClassCastException<br>...</span></span></td><td style="width: 16.6667%; height: 19px;"><b><span style="color: #333333;">롤백</span></b></td></tr></tbody></table>

![image](https://user-images.githubusercontent.com/44457591/103440343-00f12900-4c88-11eb-8fde-f336c07fb365.png)


### 결론은 스프링부트에서 트랜잭션은 Error와 RuntimeExcption 이 발생하면 롤백을 한다 ~

### (Default 옵션인 REQUIRED 일 때)

+) 그냥 내가 궁금해서.

체크예외를 커밋 대상으로 삼는 이유는 체크 예외가 예외적인 상황에서 사용되기 보다는 리턴 값을 대신해서 비즈니스 적인 의미를 담은결과로 돌려주는 용도로 사용되기 때문이다. 라고 한다.

**하지만 원한다면 체크예외지만 롤백 대상으로 삼을 수 있다. rollbackFor또는 rollbackForClassName 속성을 이용해서 예외를 지정한다.**

---

### _이외 Transaction 동작은 ?_

_백문이 불여일견 ..... 간단하게 테스트를 만들어서 _propagation 별 _스프링부트 트랜잭션 동작을 알아보았다뤼_

스프링 트랜잭션 옵션 종류



```
/**
	 * Support a current transaction, create a new one if none exists.
	 * Analogous to EJB transaction attribute of the same name.
	 * <p>This is the default setting of a transaction annotation.
	 */
	REQUIRED(TransactionDefinition.PROPAGATION_REQUIRED),

	/**
	 * Support a current transaction, execute non-transactionally if none exists.
	 * Analogous to EJB transaction attribute of the same name.
	 * <p>Note: For transaction managers with transaction synchronization,
	 * {@code SUPPORTS} is slightly different from no transaction at all,
	 * as it defines a transaction scope that synchronization will apply for.
	 * As a consequence, the same resources (JDBC Connection, Hibernate Session, etc)
	 * will be shared for the entire specified scope. Note that this depends on
	 * the actual synchronization configuration of the transaction manager.
	 * @see org.springframework.transaction.support.AbstractPlatformTransactionManager#setTransactionSynchronization
	 */
	SUPPORTS(TransactionDefinition.PROPAGATION_SUPPORTS),

	/**
	 * Support a current transaction, throw an exception if none exists.
	 * Analogous to EJB transaction attribute of the same name.
	 */
	MANDATORY(TransactionDefinition.PROPAGATION_MANDATORY),

	/**
	 * Create a new transaction, and suspend the current transaction if one exists.
	 * Analogous to the EJB transaction attribute of the same name.
	 * <p><b>NOTE:</b> Actual transaction suspension will not work out-of-the-box
	 * on all transaction managers. This in particular applies to
	 * {@link org.springframework.transaction.jta.JtaTransactionManager},
	 * which requires the {@code javax.transaction.TransactionManager} to be
	 * made available to it (which is server-specific in standard Java EE).
	 * @see org.springframework.transaction.jta.JtaTransactionManager#setTransactionManager
	 */
	REQUIRES_NEW(TransactionDefinition.PROPAGATION_REQUIRES_NEW),

	/**
	 * Execute non-transactionally, suspend the current transaction if one exists.
	 * Analogous to EJB transaction attribute of the same name.
	 * <p><b>NOTE:</b> Actual transaction suspension will not work out-of-the-box
	 * on all transaction managers. This in particular applies to
	 * {@link org.springframework.transaction.jta.JtaTransactionManager},
	 * which requires the {@code javax.transaction.TransactionManager} to be
	 * made available to it (which is server-specific in standard Java EE).
	 * @see org.springframework.transaction.jta.JtaTransactionManager#setTransactionManager
	 */
	NOT_SUPPORTED(TransactionDefinition.PROPAGATION_NOT_SUPPORTED),

	/**
	 * Execute non-transactionally, throw an exception if a transaction exists.
	 * Analogous to EJB transaction attribute of the same name.
	 */
	NEVER(TransactionDefinition.PROPAGATION_NEVER),

	/**
	 * Execute within a nested transaction if a current transaction exists,
	 * behave like {@code REQUIRED} otherwise. There is no analogous feature in EJB.
	 * <p>Note: Actual creation of a nested transaction will only work on specific
	 * transaction managers. Out of the box, this only applies to the JDBC
	 * DataSourceTransactionManager. Some JTA providers might support nested
	 * transactions as well.
	 * @see org.springframework.jdbc.datasource.DataSourceTransactionManager
	 */
	NESTED(TransactionDefinition.PROPAGATION_NESTED);
```

> **_Required 속성_ (가장 Defaut이기 때문에 따로 선언 안해줘도 된다)  
> 이미 진행중인 트랜잭션이 있다면 해당 트랜잭션 속성을 따르고, 진행중이 아니라면 새로운 트랜잭션을 생성한다  
> **

![image](https://user-images.githubusercontent.com/44457591/103440349-15352600-4c88-11eb-95b4-3d5c8605af2d.png)
![image](https://user-images.githubusercontent.com/44457591/103440351-18c8ad00-4c88-11eb-83cc-bff157a4e6aa.png)

_saveTest 에서 트랜잭션이 실행되고.._

_가장 기본적인 동작으로 최초 트랜잭션 속성을 따른다. _

_여기선 saveTest 의 트랜잭션 속성을 따라 진행될 것이다._

결과 

![image](https://user-images.githubusercontent.com/44457591/103440361-25e59c00-4c88-11eb-930e-fa1df8061565.png)

실행중인 트랜잭션이 saveTest로만 실행되는게 보이시죵??

> **_Required \_New속성_  
> 항생 새로운 트랜잭션을 생성한다. 이미 진행중인 트랜잭션이 있다면 잠깐 보류하고 해당 트랜잭션 작업을 먼저 진행한다  
> **

![image](https://user-images.githubusercontent.com/44457591/103440366-3564e500-4c88-11eb-91a4-d12900c8d2e9.png)
![image](https://user-images.githubusercontent.com/44457591/103440372-385fd580-4c88-11eb-9840-c2c42023b8f0.png)


결과


![image](https://user-images.githubusercontent.com/44457591/103440365-30079a80-4c88-11eb-8b2f-39f72452b4f1.png)

항상 새로운 트랜잭션을 실행하므로 기존 실행되던 saveTest 를 잠깐 보류하고 testTranaction이 진행된다.

_하나만 더 해보겠다.._

> **_NEVER속성_  
> 트랜잭션이 진행중이지 않을 때 작업을 수행한다. 트랜잭션이 있다면 Exception을 발생시킨다.  
> **

![image](https://user-images.githubusercontent.com/44457591/103440380-46adf180-4c88-11eb-9baf-1db4eae38a79.png)
![image](https://user-images.githubusercontent.com/44457591/103440382-49a8e200-4c88-11eb-9699-eb0dca64da25.png)


결과 

![image](https://user-images.githubusercontent.com/44457591/103440386-50375980-4c88-11eb-8168-269ab1282080.png)

_트랜잭션이 진행중이므로 익셉션이 난다._

---

왜 스프링 트랜잭션을 써야할까?

대다수 스프링 프로젝트해서 javax.transaction; 의 Transactional 을 많이 사용한다. 

이는 단일 DB일때는 상관없으나 다중 DB의 경우는 롤백 시 문제가 생길 수 있다.

javax 의 트랜잭션은 Primary 로 설정된 트랜잭션만 동작을 해서 다중 DB인 경우에는

Primary 를 제외하고는 명시적으로 어느쪽의 트랜잭션인지를 해당하는지를 선언해줘야한다.

하지만 스프링프레임워크(org.springframework.transaction.annotation)의 Transactional은 이런처리가 잘되어있나보다. 



---

_참고한 자료들 감사감사 천재개발자분덜_

[woowabros.github.io/experience/2019/01/29/exception-in-transaction.html](https://woowabros.github.io/experience/2019/01/29/exception-in-transaction.html)

[

응? 이게 왜 롤백되는거지? - 우아한형제들 기술 블로그

이 글은 얼마 전 에러로그 하나에 대한 호기심과 의문으로 시작해서 스프링의 트랜잭션 내에서 예외가 어떻게 처리되는지를 이해하기 위해 삽질을 해본 경험을 토대로 쓰여졌습니다. 스프링의

woowabros.github.io



](https://woowabros.github.io/experience/2019/01/29/exception-in-transaction.html)

[interconnection.tistory.com/122](https://interconnection.tistory.com/122)

[

Spring Transaction Exception 상황에서 Rollback 처리하기

스프링을 사용하면서 많은 서비스들이 트랜잭션을 단위로 하는 비즈니스 로직을 구현합니다. 저는 비즈니스 로직을 구현하는 과정에서 많이들 놓치는 Exception이 발생 상황에 대해서 살펴보려고

interconnection.tistory.com



](https://interconnection.tistory.com/122)

[blog.breakingthat.com/2018/04/03/springboot-transaction-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98/](http://blog.breakingthat.com/2018/04/03/springboot-transaction-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98/)

[

SpringBoot – Transaction (트랜잭션) - 조금 늦은, IT 관습 넘기 (JS.Kim)

Transaction (트랜잭션) JDK Dynamic Proxy CGLIB Proxy SpringBoot에서의 설정 및 Transaction 속성 트랜잭션의 속성 1 (전파 – Propagation) 트랜잭션의 속성 2(격리수준-Isolation) 트랜잭션의 속성 3(Rollback, Commit 관련)

blog.breakingthat.com



](http://blog.breakingthat.com/2018/04/03/springboot-transaction-%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98/)
