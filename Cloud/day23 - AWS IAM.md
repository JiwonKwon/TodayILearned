# AWS - IAM

## 계정 종류

1) **Account Owner (Root Accout)**

콘솔 및 API 사용의 모든 권한을 갖고 있음.

가급적 사용하지 않는것이 권고됨.

최초 IAM User를 만들때 사용됨

2) **IAM User**

## 인증방식

1) 영구 자격증명

IAM User로 콘솔을 사용하거나 IAM 사용자 Access Key 페어로 CLI를 사용하는것은 **영구 자격증명**으로,

사용자가 새로 비밀번호나 Access Key페어를 재발급 받기 전까지 계속 사용할 수 있다.

따라서 자격증명이 노출될 때 보안적으로 문제가 있을 수 있다.

2) 임시 자격증명

시간 제한이 있는 세션토큰이 정해져있다. 기한 만료후에는 토큰을 재발급 받아야 다시 AWS API에 연결할 수 있음. 보안적으로 강화된다.

유저ID, Password → Permenant 자격증명 (Console 로그인 등)

임시 보안 자격 증명 → Life Time 을 갖는 토큰을 이용한 임시적 자격증명

## 접근제어

인증 (Identity) : 클라이언트가 자신이 주장하는 사용자와 같은 사용자 인지 확인

인가 (Access Management) : 클라이언트가 하고자 하는 작업이 허가된 작업인지를 확인하는 권한 부여 작업

### IAM User의 인증(Identity) 방식

<img width="563" alt="1" src="https://user-images.githubusercontent.com/44457591/213920983-d6b3dc40-69bf-4178-b6f3-fb4008e7b5d8.png">

1) AWS에서의 API인증

<img width="703" alt="2" src="https://user-images.githubusercontent.com/44457591/213921018-98c2ca91-8756-411d-a575-36cf4c068c1a.png">

Credential = Access Key 로 요청주체 (IAM Principal=보안주체)를 인증한다.

Signature = Secret Key

2) Console 에서 비밀번호로 인증

### → 둘 다 장기 Credential을 사용해서 인증하는 방법.

장기 Credential을 코드에 하드코딩해서 사용하는 경우 Key 노출 시 보안사고로 이어질 수 있다.

IAM Role 의 인증(Identity) 방식

IAM Role을 사용하면 사용자 권한을 공유하거나 매번 권한이 필요할때 임시 Token을 발급받아 사용함.

자동으로 로테이션되는 임시 자격증명을 사용한다.

Access Key + Secret Key + (Token)

→ **임시 Credential을 사용해서 인증하는 방법. 이렇게 사용하는 것을 “Assume 한다” 라고 한다. Assume 하게 되면 임시 Credential을 발급받아서 특정 액션을 수행할 수 있게 된다.**

굉장히 잘 정리되어있는 AWS 교육자료

[https://whchoi98.gitbook.io/aws-iam/](https://whchoi98.gitbook.io/aws-iam/)

# IAM 정책(Policy)과 권한(Permission)

AWS의 정책 타입은 6가지로 분류된다.

1) **자격 증명 기반 (Identity-based policies)AWS 관리형 정책**

**- AWS에서 제공하는 글로벌 적용AWS 고객 관리형 정책**

**- 고객의 계정에서 생성해서 사용관리.AWS 인라인 정책**

**- 단일 사용자, 그룹, 역할(Role)에 직접 추가하는 방식.**

2) 리소스 정책 기반 (Resource-based policies)

3) **권한 경계 기반 정책 (Permissions boundaries)**

4) 조직 SCP 기반 정책 (Organizations SCPs)

5) 액세스 제어 리스트 (Access control lists -ACLs)

6) 세션 정책 (Session policies)

이러한 정책을 사용자 혹은 역할에게 부여한 상태가 그 리소스에 대한 권한이다.

<img width="599" alt="3" src="https://user-images.githubusercontent.com/44457591/213921032-11b311a0-3ab9-434c-852e-166e56ddf6e2.png">

# IAM 역할(Role)과 Switch

역할은 신뢰하는 개체(자격증명이 흭득된 상태)에 권한(Permission)을 부여하는 안전한 방법이다.

<img width="443" alt="4" src="https://user-images.githubusercontent.com/44457591/213921042-4bf241ef-2818-4874-a5b4-f912016178bf.png">

역할은 하나 이상의 정책을 기반으로 구성된다.

역할은 다음의 주체들이 사용할 수 있습니다.

**1) 동일한 AWS 계정의 IAM 사용자**

**2) 역할과 다른 AWS 계정의 IAM 사용자**

**3) Amazon Elastic Compute Cloud(Amazon EC2)와 같은 AWS가 제공하는 웹 서비스**

**4) SAML 2.0, OpenID Connect 또는 사용자 지정 구축 자격 증명 브로커와 호환되는 외부 자격 증명 공급자(IdP) 서비스에 의해 인증된 외부 사용자**

이 역할을 사용하는 것을 Assume 한다고 한다.
