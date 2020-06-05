---
layout  : wiki
title   : 
summary : 
date    : 2020-06-03 14:24:49 +0900
updated : 2020-06-04 19:49:50 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## 고객사 시스템 구축

- 협업해서 개발하고 프로젝트님은 빠지고 데브옵스팀은 남는다
- 메일 형식으로
    - CC는 누구까지 달아야 할까
    - 주간업무 수준

### 클라우드 환경 구성

- 쿠버네티스 기반의 Elastic Kubernetes Service를 운영하기 전, AWS 클라우드 환경을 구축합니다.
- 하나의 VPC에 이중화된 subnet을 구성한 후 Bastion host를 생성합니다.
    - AWS IAM 계정 생성과 MFA 설정
    - VPC 구축
    - Bastion host와 NAT instance 생성과 security group 설정

#### AWS IAM User 생성과 MFA 설정

##### [IAM User 생성](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_users_create.html)

- Identify and Access Management - 권한부여 설정
    - 루트 사용자가 IAM 사용자 생성, 권한 설정
    - AWS 액세스 유형 선택
        - AWS Management Console 액세스 선택 체크
    - 권한 설정
        - 그룹 생성
            - AdminstratorAccess 선택

<img width="939" alt="1" src="https://user-images.githubusercontent.com/48748376/83724170-c59f6500-a67a-11ea-8b02-7cfe81070276.png">

##### [멀티 팩터 인증(MFA) 사용하기](https://docs.aws.amazon.com/ko_kr/IAM/latest/UserGuide/id_credentials_mfa.html)

- Multi-Factor Authentication - 비번 이외 보안을 강화할 다른 수단(예: OTP와 비슷한 Google Authenticator 등)
- MFA 관리
    - 멀티팩터 인증(MFA)
        - 가상 MFA 디바이스
        - 스마트폰에서 Google OTP 앱 다운로드 후 QR코드 스캔
        - MFA 코드 2개가 몇 초 간격 제시됨

<img width="1237" alt="스크린샷 2020-06-03 오후 3 06 28" src="https://user-images.githubusercontent.com/48748376/83616482-dc828080-a5c2-11ea-8cc2-6e43f281b352.png">

<img width="944" alt="2" src="https://user-images.githubusercontent.com/48748376/83724179-c9cb8280-a67a-11ea-80f4-2ebe9bdf4306.png">

##### Link

- [IAM 유저 및 MFA 생성하기](https://tech.cloud.nongshim.co.kr/2018/10/11/%EC%B4%88%EB%B3%B4%EC%9E%90%EB%A5%BC-%EC%9C%84%ED%95%9C-aws-%EC%9B%B9%EA%B5%AC%EC%B6%95-2-iam-%EC%9C%A0%EC%A0%80-%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0/)

#### [VPC 구축](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/VPC_Subnets.html)

```
- VPC CIDR: 10.0.0.0/16
- Subnet
public subnet A 10.0.0.0/24
public subnet B 10.0.1.0/24
private subnet A 10.0.2.0/24
private subnet B 10.0.3.0/24
```

- AWS VPC(Virtual Private Cloud)
    - AWS 계정 전용 가상 네트워크(논리적으로 격리된 공간을 프로비저닝)
    - VPC를 만들고 가용 영역에 서브넷 추가 가능
        - VPC IPv4 주소의 범위를 [CIDR(Classless Inter-Domain Routing)](https://tools.ietf.org/html/rfc4632) 블록 형태로 지정(예: 10.0.0.0/16)
            - 사설망 대역 : 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16
            - CIDR 블록 - IP 범위를 지정하는 방식(VPC 내 자원들은 VPC의 CIDR 범위 내에서 ip를 할당받음)
            - 라우터 테이블의 기본 규칙이 VPC CIDR 블록에서 찾기 때문에 사설망이 아닌 CIDR을 사용하면 인터넷과 연결되 라우트 규칙을 정의하더라도 통신 불가
            - 각 서브넷은 단일 가용 영역 내 존재해서 다른 가용 영역 장애 격리 가능
- Public Subnet & Private Subnet
    - 퍼블릭 - Nat 인스턴스를 통해 Private Subnet의 인스턴스가 인터넷 가능하도록 만듦
    - 프라이빗 - 외부와 차단 인터넷 inbound/outbound 불가능, 다른 서브넷과 연결만 가능

<img width="970" alt="3" src="https://user-images.githubusercontent.com/48748376/83724181-ca641900-a67a-11ea-9f61-f6cc69a8cf57.png">

#### subnet 생성

- VPC를 CIDR 블록을 가지는 단위로 나누어 더 많은 네트워크 망을 만들 수 있음
- 실제 리소스가 생성되는 물리적인 공간
- VPC CIDR 블록 범위 안에서 지정 가능
- 인터넷과 연결되면 Public subnet 아니면 Private subnet
- 멀티 AZ 사용 이유
    - 멀티AZ란 하나 이상의 Availability Zone에 유사한 리소스를 동시에 배치하는 기능. AZ는 물리적 공간으로 분리돼 있기 때문에 이중화 구성해 하나의 AZ에 장애가 발생하더라도 서비스에 문제 없으면 추가비용도 없음

<img width="453" alt="4" src="https://user-images.githubusercontent.com/48748376/83724186-cafcaf80-a67a-11ea-8fd3-bb861991a26b.png">

- subnet 생성
    - Subnet과 가용존
        - Public subnet A : 10.0.0.0/24
            - ap-northeast-2a
        - Public subnet B : 10.0.1.0/24
            - ap-northeast-2c
        - Private subnet A : 10.0.2.0/24
            - ap-northeast-2a
        - Private subnet B : 10.0.3.0/24
            - ap-northeast-2c
- VPC : 앞에서 만든 VPC 사용

<img width="868" alt="스크린샷 2020-06-03 오후 4 18 47" src="https://user-images.githubusercontent.com/48748376/83616487-ddb3ad80-a5c2-11ea-9280-ff627b0aae6e.png">

#### Route Table

- Route Table
    - Router - 목적지
    - Route Table - 목적지의 이정표
    - 데이터 요청 -> 라우터 -> 라우트 테이블에서 정의한 범위 내에서 목적지 찾음
    - External-rt : Public subnet의 라우트 테이블
    - Internal-rt : Private subnet의 라우트 테이블

<img width="700" alt="5" src="https://user-images.githubusercontent.com/48748376/83724188-cb954600-a67a-11ea-83c4-6bc50a6bd5f0.png">

-  Route Table - Public subnet의 Route Table
    - 라우팅 테이블 생성
    - 이름: External-rt
    - VPC : 앞에서 만든 VPC 사용
    - 서브넷연결 : Public subnet A, B 추가

-  Route Table - Private subnet의 Route Table
    - 라우팅 테이블 생성
    - 이름: Internal-rt
    - VPC : 앞에서 만든 VPC 사용
    - 서브넷연결 : Private subnet A, B 추가

<img width="469" alt="6" src="https://user-images.githubusercontent.com/48748376/83724189-cb954600-a67a-11ea-86dc-83222e193390.png">

#### Internet Gateway

- VPC는 격리된 네트워크 환경이기 때문에 VPC에서 생성된 리소스들은 인터넷 사용 불가
- VPC와 인터넷을 연결해주는 관문
- 목적지의 주소가 10.0.0.0/16에 매칭되는지 확인 후 없으면 인터넷 게이트웨이로 보냄

<img width="695" alt="7" src="https://user-images.githubusercontent.com/48748376/83724190-cc2ddc80-a67a-11ea-926c-c3d069bac280.png">

- 인터넷 게이트웨이 생성
    - VPC에 연결 - 앞에서 만든 VPC 사용
    - Route Table(External-rt)에 인터넷 게이트웨이 추가
        - 대상: 0.0.0.0/0, 생성한 인터넷 게이트웨이

<img width="843" alt="8" src="https://user-images.githubusercontent.com/48748376/83724196-ce903680-a67a-11ea-962c-76c71d06e445.png">

#### Nat Gateway

- Private Subnet이 인터넷과 통신하기 위한 아웃바운드 인스턴스
- Private Subnet은 외부에서 요청하는 인바운드는 차단하더라도 아웃바운드 트래픽 허용 필요- Private Subnet에서 외부로 요청하는 아웃바운드 트래픽을 받아 Internet Gateway와 연결

<img width="662" alt="9" src="https://user-images.githubusercontent.com/48748376/83724194-ce903680-a67a-11ea-9449-03431a237e30.png">

##### Link

- [네트워크 구성하기(VPC, Subnet, Route Table, Internet Gateway)](https://tech.cloud.nongshim.co.kr/2018/10/16/4-%eb%84%a4%ed%8a%b8%ec%9b%8c%ed%81%ac-%ea%b5%ac%ec%84%b1%ed%95%98%ea%b8%b0vpc-subnet-route-table-internet-gateway/)
- [가장쉽게 VPC 개념잡기](https://medium.com/harrythegreat/aws-%EA%B0%80%EC%9E%A5%EC%89%BD%EA%B2%8C-vpc-%EA%B0%9C%EB%85%90%EC%9E%A1%EA%B8%B0-71eef95a7098)

#### [Bation host와 NAT instance 생성](https://docs.aws.amazon.com/ko_kr/vpc/latest/userguide/VPC_NAT_Instance.html)

- Bastion Host
    - 내부와 외부 네트워크 사이에서 게이트 역할을 하는 Host
    - 외부에서 접근 가능하도록 Public IP 부여

- NAT instance
    - NAT Gateway 유료(0.059$ -> 1달 43.896$)이므로 Nat Instange로 대체
    - 비용문제로 Nat instance를 Bastion Host로 사용

- VPC 퍼블릭 서브넷에 있는 네트워크 주소 변환(NAT) 인스턴스를 사용
    - 프라이빗 서브넷의 인스턴스가 인터넷 가능
    - NAT는 IPv6 트래픽 지원 불가(외부 전용 인터넷 게이트웨이 사용)


- EC2 - 인스턴스 시작 - 커뮤니티 AMI 탭- nat 검색
    - 커뮤니티 AMI는 AWS 개발자 커뮤니티 회원 업로드(무료)
    - 마켓플레이스는 유료

##### Nat 인스턴스 생성

- AMI 선택 > 커뮤니티 AMI > NAT 검색(맨 위의 AMI 사용)
    - AMI : amzn-ami-vpc-nat-hvm-2017.09.1-testlongids.20180307-x86_64-ebs - ami-0185fd13b4270de70
    - 인스턴스 타입 : t2.micro(free-tier)
    - 스토리지 추가 : 8GB, gp2
    - 태그 : 미설정
    - 보안 그룹 :
    - 유형          | 프로토콜 | 포트범위 | 소스
    - SSH           | TCP      | 22       | MyIP
    - All Traffic   | ALL      | 0-65535  | VPC CIDR
    - 검토 및 시작
    - 키 페어 선택 > 인스턴스 시작

- My IP에 고정 아이피 할당
    - [AWS Elastic IP (EIP) 고정 아이피 할당 하기](https://goddaehee.tistory.com/m/192)

<img width="592" alt="10" src="https://user-images.githubusercontent.com/48748376/83724192-cd5f0980-a67a-11ea-9116-19fab7648c3f.png">

- Route Table(Internal-rt)에 추가
    - 대상 0.0.0.0/0, Nat instance
- 네트워킹 > 소스/대상 확인 변경

<img width="1016" alt="11" src="https://user-images.githubusercontent.com/48748376/83724191-ccc67300-a67a-11ea-904d-665c8d9b84e5.png">


##### OS 계정 생성과 비밀번호 로그인 허용

- 보안상 운영 서버에서 ec2-user 사용 금지
- 개인 계정 ex)admin을 생성한 후 비밀번호를 설정하여 로그인할 때 id와 pw를 통해 접속 (1인 1계정 사용 이유: log가 남기 때문에 장애 발생시 책임의 소재를 명확히 할 수 있다.)
- ssh 접속시 비밀번호 로그인 허용 설정

```python
# 루트로 계정 변경
$ sudo -i 

# 계정 생성
$ useradd admin

# 비밀번호 입력
$ passwd admin

# 생성된 계정 확인
$ cat /etc/passwd
```

- sudo 권한 부여

```python
# 루트로 계정 변경
$ sudo -i

# ec2-user와 동일한 권한 부여
$ vi /etc/sudoers.d/cloud-init

# 파일 저장
$ admin ALL=(ALL) NOPASSWD:ALL
```

- 비밀번호 로그인 설정
    - 키 방식 로그인 차단, 비밀번호 로그인 허용

```python
# 루트로 계정 변경
$ vi /etc/ssh/sshd_config

PasswordAuthentication yes  # 이 부분 주석 해제
# PasswordAuthentication no # 이 부분 주석 설정


# sshd 데몬 재시작
$ service sshd restart
```

##### Link

- [클라우드 코멘토]


