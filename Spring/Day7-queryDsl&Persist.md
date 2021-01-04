# day7

## queryDsl에서 Projection을 통해 Entity를 DTO로 만들기

[https://www-swpro-com.tistory.com/9](https://www-swpro-com.tistory.com/9)

그러던중..

ERROR1

```bash
org.springframework.dao.InvalidDataAccessApiUsageException: No sources given; nested exception is java.lang.IllegalArgumentException: No sources given
```

No source...?

문제는 프롬절을 빼먹은 것 이었다..^^ 삽주세요 삽 ~~

```
return queryFactory
        .select(Projections.constructor(MemberOAuth.class,
                mbMemOAuth.memNo,
                mbMemOAuth.svcNm,
                mbMemOAuth.svcUsrId))
        .from(mbMemOAuth)  // 프롬절
        .where(mbMemOAuth.memNo.eq(memberNo))
        .fetchOne();
```

그리고 멍충이 같이 insert 왜 없어.. 왜안돼.. .? 이러고 있었다...

아무생각없이 2년간 코딩한 결과인가요..?^^ 이제부터 생각하면 되지 ~~~~~~~~~^^ 생각생각생각

```java
private final EntityManager entityManager;

@Override
    public void insertMember(MemberOAuth memberOAuth) {
        MbMemOAuth mbMemOAuth = MbMemOAuth.builder()
                .svcNm(memberOAuth.getServiceName())
                .svcNm(memberOAuth.getServiceUserId())
                .build();

        entityManager.persist(mbMemOAuth);
        entityManager.flush();
        entityManager.clear();
    }
```

EntityManager를 이용하여 insert 해줌.

MbMemOAuth에는 `Setter`를 넣지않고 `Builder 패턴`을 사용했다.

Setter를 무분별하게 사용하면 객체의 일관성을 보장하기 어렵다.

[https://velog.io/@aidenshin/내가-생각하는-JPA-엔티티-작성-원칙](https://velog.io/@aidenshin/%EB%82%B4%EA%B0%80-%EC%83%9D%EA%B0%81%ED%95%98%EB%8A%94-JPA-%EC%97%94%ED%8B%B0%ED%8B%B0-%EC%9E%91%EC%84%B1-%EC%9B%90%EC%B9%99)

ERROR2

```bash
org.springframework.dao.InvalidDataAccessApiUsageException: detached entity passed to persist:
```

```java
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
public class MbMemOAuth extends BaseTimeEntity {

    @Id
    @Column(name = "MEM_NO")
    private Long memNo;

    @Column(name="SVC_NM")
    private String svcNm;

    @Column(name="SVC_USER_ID", nullable = false)
    private String svcUsrId;

    @Builder
    public MbMemOAuth(long memNo, String svcNm, String svcUsrId) {
        this.memNo = memNo;
        this.svcNm = svcNm;
        this.svcUsrId = svcUsrId;
    }
}
```

`@GeneratedValue(strategy = IDENTITY)`

이 라인 때문이었다 ㅠㅠ

순서대로 1 , 2 , 3 늘려주는건데 영속성을 보장하지 못하는가보다.

영속성 넘어렵다

공부할거 추가 !


+)

```
spring:
  profiles: local
  datasource:
    data: classpath:data/data-h2.sql # 시작할때 실행시킬 script
```

boot 시작시 h2에 data 생성이 제대로 되지 않았음

org.hibernate.resource.transaction.backend.jdbc.internal.DdlTransactionIsolatorNonJtaImpl getIsolatedConnection

h2의 버전 문제였따

[https://www.inflearn.com/questions/17219](https://www.inflearn.com/questions/17219)

build.gradle

compile 'com.h2database:h2:1.4.199'

버전을 변경하니 정상적으로 실행됨

+) Entity의 CamelCase 대문자는 테이블 _ 언더바를 구별해줌

MbMemOauth ⇒ MB_MEM_OAUTH로 테이블 생성해준다.
