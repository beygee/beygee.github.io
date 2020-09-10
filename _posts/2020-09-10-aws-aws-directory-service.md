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

AWS VPC 내에 고 가용성을 위하여 `Domain Controllers`를 멀티 AZ에 설치할 수 있다. **최소 2 AZ**에 설치할 수 있으며, 원한다면 더 스케일링 아웃시킬 수 있다.
RDS for SQL, AWS Workspace, Quicksight 등과 통합될 수 있으며, AWS SSO를 이용하여 3rd Party AWS Application에 모두 접근 가능하다.
![image-right](/assets/images/2020-09-10-01.png){: .align-right}

### AWS Managed Microsoft AD - Integrations

AWS Managed AD DC가 어떻게 다양한 서비스와 통합 될 수 있는지 아래 이미지가 상세히 설명해주고 있다.

![image-right](/assets/images/2020-09-10-02.png)

