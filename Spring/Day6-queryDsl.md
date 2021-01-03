# day6

## Entity에 일일히 수정시간,생성시간을 추가하지 않는 방법

```yaml
classpath "io.spring.gradle:dependency-management-plugin:1.0.4.RELEASE"

//Spring Boot Overriding
ext['hibernate.version'] = '5.2.11.Final' //추가
```

gradle에 의존성 두가지 추가하고

BaseTimeEntity를 생성했다.

```java
package com.jidong.ccadui.domain;

import java.time.LocalDateTime;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;
import lombok.Data;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

@Data
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public abstract class BaseTimeEntity {
    @CreatedDate
    private LocalDateTime createDt;

    @LastModifiedDate
    private LocalDateTime updateDt;

}
```

```yaml
spring:
  profiles:
    active: local # 기본 환경 선택
  jpa:
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL5InnoDBDialect

##스프링버전 2.2.0 이후부터는 아래를 추가해줘야함
  datasource:
    hikari:
      jdbc-url: jdbc:h2:mem://localhost/~/testdb;MODE=MYSQL
```

## 운영DB 환경구성

**1) 운영 DB는 외부 폴더에 구성**

```yaml
# 운영 환경
---
spring.profiles: set1
spring.profiles.include: real-db

server:
  port: 8081

---
spring.profiles: set2
spring.profiles.include: real-db

server:
  port: 8082
```

real-db는 프로젝트 외부에 구성

```yaml
---
spring:
  profiles: real-db
  datasource:
        url: jdbc:mysql://%%%%-aws-db.cvvxvmi46zdw.ap-northeast-2.rds.amazonaws.com:3306/%%%%
        username: $$$$
        password: $$$$
        driver-class-name: org.mysql.jdbc.Driver
```

[https://jojoldu.tistory.com/372](https://jojoldu.tistory.com/372)

**2) 운영환경 jpa 구현을 위한 queryDsl 추가**

1) build.gradle에 queryDsl 관련 의존성 주입

SpringBoot 버전 2.3.1

gradle 버전 1.0.4

queryDsl 업데이트가 버전에 따라 넘 안되고있어서 환경맞추기 힘들었따. 후 ..^^

build.gradle

```yaml
buildscript {
    ext {
        querydslPluginVersion = '1.0.10' // 플러그인 버전
    }
    dependencies {
        classpath "io.spring.gradle:dependency-management-plugin:1.0.4.RELEASE"
        classpath("gradle.plugin.com.ewerk.gradle.plugins:querydsl-plugin:${querydslPluginVersion}") // querydsl 플러그인 의존성 등록
    }
}

dependencies {
	compile("com.querydsl:querydsl-jpa") // querydsl
  compile("com.querydsl:querydsl-apt") // querydsl
}

// querydsl 적용
apply plugin: "com.ewerk.gradle.plugins.querydsl" // Plugin 적용
def querydslSrcDir = 'src/main/generated'
querydsl {
    library = "com.querydsl:querydsl-apt"
    jpa = true
    querydslSourcesDir = querydslSrcDir
}

compileQuerydsl {
    // querydsl 컴파일시 사용하는 애노테이션프로세서('com.querydsl.apt.jpa.JPAAnnotationProcessor')의 경로를 querydsl 이 지정한 경로를 이용한다는 선언
    options.annotationProcessorPath = configurations.querydsl
}

configurations { querydsl.extendsFrom compileClasspath }
sourceSets {
    main { java { srcDirs = ['src/main/java', querydslSrcDir] } }
}

tasks.withType(JavaCompile) {
    options.annotationProcessorGeneratedSourcesDirectory = file(querydslSrcDir)
}
```

2) QuerydslConfig 클래스 생성

```java
package com.jidong.ccadui.config;

import com.querydsl.jpa.impl.JPAQueryFactory;
import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class QuerydslConfig {
    @PersistenceContext
    private EntityManager entityManager;

    @Bean
    public JPAQueryFactory jpaQueryFactory() {
        return new JPAQueryFactory(entityManager);
    }
}
```

JPA 개발을 위해 queryDsl 설정까지 완료  ~ 낼은 본격적으로..^^ 개발코드를 운영에 올려놓고 테스트 해보겠따.
