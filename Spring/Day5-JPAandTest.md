# day5

day3 과 day4를 (12/31 과 1/1의 기록) 을 미리 써둔 블로그글로 올려서 쪼끔 양심에 찔리는것..^.^

오늘은 진행하고 있던 달력프로젝트의 db 테스트환경 구성과 운영환경 구성을 진행했다.

## 로컬에서의 테스트 (h2)

원래 myBatis를 사용하려 했지만, 복잡한 쿼리가 많이 쓰이는 것도 아니고 테스트도 내기준 용이하지않아서 jpa를 사용하기로 했다.

1) Member Entity 생성

```java
package com.jidong.ccadui.domain.member.repository;

import static javax.persistence.GenerationType.IDENTITY;
import static javax.persistence.GenerationType.SEQUENCE;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;
import lombok.AccessLevel;
import lombok.Builder;
import lombok.*;
import lombok.NoArgsConstructor;

@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Getter
@Entity
public class Member {

    @Id
    @GeneratedValue(strategy=IDENTITY)
    private long memNo;

    @Column
    private String serviceName;

    @Column(nullable = false)
    private String serviceUserId;

    @Builder
    public Member(long memNo, String serviceName, String serviceUserId) {
        this.memNo = memNo;
        this.serviceName = serviceName;
        this.serviceUserId = serviceUserId;
    }
}
```

Entity 클래스를 생성하실때, 주의하실것은 무분별한 setter 메소드 생성

```
@NoArgsConstructor
```

: 기본 생성자 자동 추가

- `access = AccessLevel.PROTECTED` : 기본생성자의 접근 권한을 protected로 제한
- 생성자로 `protected Member() {}`와 같은 효과
- Entity 클래스를 **프로젝트 코드상에서 기본생성자로 생성하는 것은 막되**, **JPA에서 Entity 클래스를 생성하는것은 허용**하기 위해 추가

여기서 생성자 대신에 `@Builder`를 통해 제공되는 빌더 클래스를 사용합니다.생성자나 빌더나 **생성시점에 값을 채워주는 역할은 똑같습니다**.다만, 생성자의 경우 지금 **채워야할 필드가 무엇인지 명확히 지정할수가 없습니다**.예를 들어 아래와 같은 생성자가 있다면

```
public Example(String a, String b){
    this.a = a;
    this.b = b;
}
```

개발자가 `new Example(b,a)`처럼 a와 b의 위치를 변경 해도 실제로 코드를 실행하기전까진 전혀 문제를 찾을수가 없습니다.하지만 빌더를 사용하게 되면 아래와 같이

```
Example.builder()
    .a(a)
    .b(b)
    .build();
```

어느 필드에 어떤 값을 채워야 할지 명확하게 인지할 수 있습니다.

아하..! 명확하게 인지하는 장점때문에 빌더패턴을 사용한거구나 ^0^ ~~~

2) MemberRepository 생성

보통 ibatis/MyBatis 등에서

**Dao**

라고 불리는 DB Layer 접근자입니다.

JPA에선 Repository라고 부르며

**인터페이스**

로 생성합니다.

단순히 인터페이스를 생성후,

```
JpaRepository<Entity클래스, PK타입>
```

를 상속하면 기본적인 CRUD 메소드가 자동생성 됩니다.

특별히

**`@Repository`를 추가할 필요도 없습니다.**

```java
package com.jidong.ccadui.domain.member.repository;

import org.springframework.data.jpa.repository.JpaRepository;

public interface MemberRepository extends JpaRepository<Member, Long> {
}
```

junit5로 의존성 주입 후 테스트 코드 추가하고 테스트 했다.

## 운영환경에서의 테스트

내일은 진짜! 운영환경에서 돌아가게 해야지 ^^ ....

코딩하다보면 쓸데없는 곳에서 삽질의 연속 ing 라 시간이 훅훅 가버린다

밑의 짤 정말 공감 그자체

![Untitled](https://user-images.githubusercontent.com/44457591/103457737-2db74600-4d45-11eb-8299-2d4ac221b734.png)


출처 ) 완전 감사한 분

[https://jojoldu.tistory.com/251?category=635883](https://jojoldu.tistory.com/251?category=635883)
