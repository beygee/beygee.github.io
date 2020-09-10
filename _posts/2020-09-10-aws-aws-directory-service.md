---
title: "[AWS Certified SAP] AWS Directory Service"
date: 2020-09-10 23:26:28 +0900
categories: aws
---

`AWS Directory Service`를 알아보기 전에 먼저 Microsoft Active Directory(AD)를 알아보자.

## Microsoft Active Directory

`Active Directory`란, 회사 직원들의 컴퓨터, 프린터, 보안 전산 물체 등의 계정 정보(ID, password)를 통합 관리하는 데이터베이스를 의미한다. 회사에서 계정 보안 정보에 특별한 강제성을 부여할 수도 있다. 예를 들면 5분이상 컴퓨터를 사용하지 않으면 화면 보호기가 켜진다던지, 1달마다 주기적으로 비밀번호를 변경해야한다던지 등등 말이다.

이는 **파일 타입**의 데이터베이스이며 **암호화**되어 저장되어있기 때문에, 일반적인 텍스트 에디터로 열어볼 수 없다.

각각의 전산 물체는 `Object`라고 하며, 이는 계층 형태의 트리 구조로 이루어져 있다!
{: .notice}

## ADFS (AD Federation Services)

`ADFS`는 AD와 결합하여 AWS Single Sign On (SSO) 서비스를 이용하기 위한 `Cross Application`이다.

<figure>
    <img src="/assets/images/2020-09-10-00.png" />
    <figcaption>ADFS 매커니즘</figcaption>
</figure>

1. 유저는 ADFS를 통하여 기존의 AD에 접근하여 계정 정보를 조회한다.
2. ADFS에서 유저에게 전달받은 계정정보를 AD 데이터베이스에서 확인한다.
3. 유저에게 Credentials를 넘겨준다.
4. Credentials를 AWS SSO에 넘겨주어, STS 토큰을 반환받는다.
5. 해당 토큰을 이용하여 AWS 서비스를 이용하거나, 콘솔에 접근한다.

## AWS Directory Service

AWS에서 관리하는 Directory Service는 3가지 형태가 있다.

1. AWS Managed Microsoft AD
   - AWS 내에 AD를 생성하여 유저를 관리한다. **(MFA도 가능)**
   - 온프레미스 AD와도 신뢰 연결을 구축할 수 있다.
2. AD Connector
   - AWS에 요청되는 유저 조회 요청을 AWS Connector로 통하여 온프레미스 AD에 전달할 수 있다. (프록시 형태)
3. Simple AD
   - Microsoft AD 호환이 아닌 단순 AD. 이는 첫번째 케이스터럼 온프레미스 AD와 결합될 수 없다. 비용이 저렴한 반면에 확장성이 없다. **(MFA 불가)**

### AWS Managed Microsoft AD

{% include video id="XNTsmRe8k7Q" provider="youtube" %}

![image-right](/assets/images/2020-09-10-01.png){: .align-right}
AWS VPC 내에 고 가용성을 위하여 `Domain Controllers`를 멀티 AZ에 설치할 수 있다. **최소 2 AZ**에 설치할 수 있으며, 원한다면 더 스케일링 아웃시킬 수 있다.
RDS for SQL, AWS Workspace, Quicksight 등과 통합될 수 있으며, AWS SSO를 이용하여 3rd Party AWS Application에 모두 접근 가능하다.

### AWS Managed Microsoft AD - Integrations

AWS Managed AD DC가 어떻게 다양한 서비스와 통합 될 수 있는지 아래 이미지가 상세히 설명해주고 있다.

![](/assets/images/2020-09-10-02.png)

AWS AD는 온프레미스 AD와 통합될 수 있으며, DC에서 사용가능한 서비스는 다음과 같다.

- RDS for SQL Server
- Amazon WorkSpace
- Amazon Quicksight
- Amazon Connect
- Amazon WorkDocs
- Amazon Single Sign On (SSO를 통하여 다양한 3rd Party 로그인이 가능하다.)

또한 전통적인 AD Applications를 적용하여 이용이 가능하다.

### AWS Managed Microsoft AD - Connect to on-premise AD

Managed Microsoft AD에서 온프레미스 AD와 연결하는 법은 다음과 같다.

![](/assets/images/2020-09-10-03.png)

온프레미스와 VPC 사이에는 **Direct Connect**를 이용하여 연결한다. (Site-to-Site VPN도 가능)

두개의 AD 데이터베이스는 데이터가 독립적이다. 그래서 신뢰가 필요한 것이다. (AD간에 Replica는 이루어지지 않는다!)

예를 들어보면, `X` AD와 `Y` AD가 존재하는데, 이들은 Two-way forest trust 관계이다. 만약 X에 어떤 유저 정보 조회를 요청했는데 없을 경우, `X` AD는 자신이 신뢰하고 있는 `Y` AD에게 가서, 그 유저의 정보가 있는지 물어본다.
따라서 **유저 정보가 어느 AD에 있든지 간에 신뢰 관계만 형성되어 있으면 확인이 가능한 것이다.**

신뢰 관계 형성에는 3가지의 종류가 있다!

- One way trust (AWS -> On-premise)
- One way trust (On-premise -> AWS)
- Two way forest trust (On-premise <-> AWS)
  {: .notice .notice--primary}

### Solution Architecture: Active Directory Replication

좀 전에 AD간에 Replica는 이루어지지 않는다고 했다. 하지만 방법은 존재한다.

AD 간의 레이턴시를 최소화하기 위해 (Direct Connect나 VPN Latency) On-premise의 AD를 AWS VPC 내부에 EC2를 띄워 복제를 떠 놓는 것이다.

![](/assets/images/2020-09-10-04.png)

이렇게 하면 Home VPC 내부에서 레이턴시를 최소화하고 유저의 정보를 조회할 수 있다.
