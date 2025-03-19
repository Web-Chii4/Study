## 🍅Saas
SaaS(Software as a Service) 는 클라우드에서 소프트웨어를 제공하는 서비스 모델
사용자는 프로그램을 직접 설치하지 않고, 인터넷을 통해 웹 브라우저 또는 API를 이용해 서비스에 접속

<br>

### 1. SaaS의 주요 특징
>**클라우드 기반**: 온프레미스 서버 없이 클라우드에서 실행됨
**구독형 서비스**: 사용자가 필요할 때만 비용을 지불하는 방식
**다중 테넌트(Multi-Tenancy)**: 여러 고객이 하나의 애플리케이션을 공유
**자동 업데이트**: 운영자가 별도로 업데이트하지 않아도 자동 배포 가능

<br>

### 2. SaaS 서비스 예시
>**기업용**: `Microsoft 365`, `Google Workspace`, `Slack`, `Salesforce`, `Zoom`
**클라우드 제공업체 SaaS**: `AWS Lambda`, `Amazon RDS`, `AWS API Gateway`, `AWS Cognito`
**스토리지 기반 SaaS**: `Dropbox`, `Google Drive`

<br>

### 3. SaaS 보안 공격 유형별 상세 설명
#### 1. 사용자 계층 공격 (Identity & Access)
>✅ **계정 탈취 공격 (Account Takeover, ATO)**
**공격 방법**
**피싱(Phishing)** → 사용자의 인증 정보를 탈취
**크리덴셜 스터핑(Credential Stuffing)** → 유출된 계정 정보를 사용
**MFA 우회(Man-in-the-Middle MFA Bypass)**→ SMS 기반 MFA 가로채기
>
**대표적인 사례**
2023년 Microsoft 365 피싱 캠페인 (OAuth 토큰 탈취 시도)
2024년 Okta 계정 탈취 사건 (MFA 우회 및 OAuth 인증 악용)
>
**방어 방법**
`MFA(다단계 인증)` 강제 적용
`SSO(Single Sign-On)` 및 조건부 액세스(Conditional Access) 적용
`AWS IAM` & `AWS Cognito`를 통한 강력한 인증 정책 설정

<br>

#### 2. 네트워크 계층 공격 (Network Attacks)
>✅ **중간자 공격 (MITM, Man-in-the-Middle)**
**공격 방법**
공격자가 <span style=color:red>클라이언트와 서버 사이에 위치</span>하여 데이터 트래픽을 가로채거나 변조
HTTPS가 적용되지 않은 API 요청을 가로채 **OAuth 토큰** 탈취
>
**대표적인 사례**
2021년 Microsoft Teams MITM 공격 → OAuth 토큰 가로채기
2023년 SaaS VPN 트래픽 감청 사고
>
**방어 방법**
`AWS CloudFront` + `TLS 1.2/1.3` 강제 적용
`AWS WAF` & `AWS Shield` 를 통한 트래픽 보호
`HSTS(HTTP Strict Transport Security)` 적용

<br>

#### 3. 애플리케이션 계층 공격 (Application Security)
>✅ **SQL Injection (SQLi)**
**공격 방법**
웹 애플리케이션의 입력 필드를 통해 SQL 쿼리를 조작하여 데이터베이스에서 정보를 유출
SaaS에서 사용되는 고객 정보, 결제 데이터 등을 노림
>
**대표적인 사례**
2023년 SaaS 기반 CMS의 SQL Injection 취약점 악용 사례
AWS RDS 기반 서비스에서 SQL Injection을 통한 데이터 유출
>
**방어 방법**
AWS WAF에서 SQL Injection 방어 정책 활성화
`ORM(Object-Relational Mapping)` 및 `Prepared Statements` 적용
`AWS Security Hub`로 취약점 자동 감지
>
<br>
>
>✅ **XSS (Cross-Site Scripting)**
**공격 방법**
사용자가 입력한 데이터를 검증 없이 실행하여 악성 스크립트를 주입
SaaS 애플리케이션의 대시보드, 메신저, 이메일 등의 사용자 입력 부분이 주요 타겟
>
**대표적인 사례**
2023년 Microsoft 365 XSS 취약점을 이용한 OAuth 토큰 탈취
Google Workspace XSS 취약점을 통한 Gmail 계정 접근
>
방어 방법
AWS WAF에서 `XSS 필터` 적용
`Content Security Policy(CSP)` 활성화
`React/Angular` 등 프레임워크의 **자동 Escape 기능** 활용

<br>

#### 4. API 계층 공격 (API Security)
>✅ **Broken Authentication (API 인증 우회)**
**공격 방법**
OAuth 또는 JWT 기반 인증이 취약할 경우 API 호출을 변조하여 권한을 상승시킴
API Key가 GitHub에 노출되었을 경우 API 무단 접근
>
**대표적인 사례**
2024년 Google Cloud API 키 유출 사고
2023년 Slack OAuth 인증 토큰 유출로 인해 SaaS 데이터 접근
>
**방어 방법**
`AWS API Gateway` + `AWS Cognito` 연동하여 OAuth2.0 기반 인증 적용
JWT 토큰의 만료 시간 단축 및 서명 검증 강화
`AWS Secrets Manager`로 API Key 안전하게 저장

<br>

#### 5. 데이터 계층 공격 (Data Security)
>✅ **S3 Misconfiguration (S3 버킷 설정 오류)**
**공격 방법**
퍼블릭으로 잘못 설정된 AWS S3 버킷을 스캔하여 내부 데이터 유출
API Key 또는 사용자 데이터가 포함된 파일을 다운로드하여 악용
>
**대표적인 사례**
2023년 Microsoft의 고객 지원 데이터가 S3 설정 오류로 노출됨
2022년 AWS에서 운영되는 SaaS의 의료 데이터 유출
>
**방어 방법**
`AWS Macie`로 S3 버킷 설정 점검 및 민감 데이터 탐지
`AWS KMS`로 저장 데이터 암호화 활성화
`S3 버킷` 퍼블릭 액세스 차단 및 `Access Control List(ACL)` 적용

<br>

#### 6. 공급망 공격 (Supply Chain Attacks)
>✅ **Dependency Confusion (패키지 공급망 공격)**
**공격 방법**
SaaS 애플리케이션이 사용하는 패키지의 네이밍 충돌을 이용하여 악성 코드 삽입
공격자가 npm, PyPi, Maven 등의 오픈소스 패키지에 악성 코드를 심어 SaaS 내부에서 실행되도록 유도
>
**대표적인 사례**
2021년 Microsoft, Apple, Tesla의 Dependency Confusion 공격 노출
2023년 AWS Lambda에서 사용하는 패키지의 악성 코드 발견
>
**방어 방법**
`AWS CodeArtifact`로 내부 패키지 레지스트리 운영
`IAM 정책`을 이용하여 서드파티 패키지 설치 제한
`Snyk`, `SonarQube` 같은 보안 스캐너 활용하여 코드 검증

<br>

### 4. SaaS 보안 위협 및 대응 솔루션
![](https://velog.velcdn.com/images/tlaals44/post/2313b1a8-2173-42c4-8061-b72042e9a34f/image.png)

<br>