# day8

## compileQueryDsl했을 시 빌드 실패

Q클래스는 만들어졌으나 자꾸 빌드실패했다.

어제 새벽2시까지 뻘짓하고 진짜 열받아버렸는데 build.gradle 계속 수정하고,, ㅠ ㅠ 

근데 진짜 허망하게도 import문 한땀한땀 다시 쓰니까 제대로 빌드됐다..^^ ~

아 그리고 build.gradle 에는 이렇게 추가함

`id "io.franzbecker.gradle-lombok" version "3.0.0"`

롬복과 queryDsl 같이 사용할 때 에러가 발생할 수 있다고 한다.

도움을 준 출처 : [https://www.inflearn.com/questions/23530](https://www.inflearn.com/questions/23530)

드뎌 빌드는 다시 성공했다 ^0^ 

[https://travis-ci.org/github/JD-PROJ/cca-dui-api/builds/752989820](https://travis-ci.org/github/JD-PROJ/cca-dui-api/builds/752989820)

또 테스트 코드 작성중에 jpa 영속성 에러와 Entity primaryKey가 세팅이 안되는 중이다 .. 

이건 낼 좀더 보겠따