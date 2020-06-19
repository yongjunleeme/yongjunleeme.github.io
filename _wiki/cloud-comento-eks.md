---
layout  : wiki
title   : 
summary : 
date    : 2020-06-10 18:21:56 +0900
updated : 2020-06-18 23:29:22 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Background

- 쿠버네티스 기반의 EKS 환경 구성과 웹 서비스 구축
    - EKS 클러스터와 노드그룹을 생성
        - EKS cluster 생성
        - Worker node group 생성
    - 구축된 쿠버네티스 환경에 Nginx 서비스를 배포
        - Nginx 서비스 배포

- NACL
    - 네트워크 ACL은 주고(outbound) 받는(inbound) 트래픽을 제어하는 가상 방화벽입니다. 시큐리티 그룹은 인스턴스의 앞단에서 트래픽을 제어하는 가상 방화벽인 반면, 네트워크 ACL은 서브넷 앞단에서 트래픽을 제어하는 역할을 합니다. 따라서 네트워크 ACL의 규칙을 통과하더라도 시큐리티 그룹의 규칙을 통과하지 못 하면 인스턴스와는 통신하지 못 할 수 있습니다. 


### Kubernetes

- 컨테이너 오케스트레이션 툴(Docker Swarm 같은..)
- 컨테이너를 쉽고 빠르게 배포/확장하고 관리를 자동화해주는 오픈소스 플랫폼 = 컨테이너 관리 툴
    - 블루그린 배포, 카나리 배포 등을 실현할 수 있도록 Deployment, Daemonset같은 다양한 배포 방식을 지원하고 Auto Scaling 등을 지원

#### 쿠버네티스 클러스터 아키텍처

<img width="543" alt="1" src="https://user-images.githubusercontent.com/48748376/84260161-87131a00-ab54-11ea-97d9-2b3e832e010f.png">

- [Amazon EKS사용 설명서](chrome-extension://cbnaodkpfinfiipjblikofhlhlcickei/src/pdfviewer/web/viewer.html?file=https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/eks-ug.pdf)


- 크게 마스터와 워커노드로 구분
- 여러 대의 서버가 하나의 클러스터로 연결
- 쿠버네티스 마스터 -> 컨트롤 플레인이 실행됨, 클러스터의 두뇌
    - 컨테이너 스케줄링, 서비스 관리, API 요청 수행 (파드, 리소스 컨트롤러, 로드밸런서 관리)
- 쿠버네티스 워커노드 -> 사용자의 워크로드 실행 마스터의 관리 아래 실제 pod같은 리소스가 생성되는 노드
- 노드 안에 파드 리소스


#### 관리형 Kubernetes vs 자체 호스팅

- Kubernetes를 자체적으로 구축가능(= 자체 호스팅)
    - 구축, 지속적 관리 필요(업데이트 빠름)
- 따라서 관리형 쿠버네티스 추천 → AWS의 EKS, Google의 GKE, Azure의 AKS
    - 고가용성이 보장된 쿠버네티스 클러스터 제공(위의 아키텍처)

#### Kubernetes Object

- 쿠버네티스는 상태를 관리하기 위한 대상을 오브젝트로 정의
    - Pod – 쿠버네티스의 가장 작은 배포 단위, 컨테이너의 모임, 하나 이상의 컨테이너로 구성
    - Deployment – 애플리케이션 배포의 기본 단위가 되는 리소스
    - Service - Pod를 외부에 노출시켜주는 로드밸런서
    - 위의 오브젝트들은 yaml 파일로 정의하여 kubectl 명령어로 반영한다.
- Node(=WorkerNode)는 EKS의 NodeGroup.(하나의 컴퓨터라고 생각)
- 컨테이너(web application 등)를 담은 Pod는 Deployment에 의해 관리된다.
- Deployment에서 몇 개의 파드가 얼마 만큼의 자원을 사용할지 정의한다.
    - 파드들은 어떻게 롤아웃될 것인지
    - cpu나 메모리 어떻게 사용할지
    - 설명서 느낌
    - `nginx-deployment.yaml`
- Pod는 각각의 WorkerNode에 배포되고 서비스를 통해 외부에 노출된다. (사람들이 인터넷을 통해 접근할 수 있게 외부로 노출)


- pod는 각자 ip 할당
    - pod 하나에 하나의 서비스를 담아야 ip를 아껴서 사용할 수 있다
    - 반대로 우리는 MSA하겠다 다 따로 담을 수도 있다
    - 내부에 구성, 외부에서 쓸 수 없다
        - 인터넷으로 접근 가능 - 서비스라는 쿠버네티스 자원
            - 로드 밸런서 타입
                - 로드밸런서 -> 서비스 -> 파드로 포워딩
- 어떻게 마스터와 노드 소통?
    - kube apiserver의 엔드포인트로 노그그룹과 통신


<img width="440" alt="2" src="https://user-images.githubusercontent.com/48748376/84260167-8a0e0a80-ab54-11ea-96e2-4e7d91161974.png">

#### Ngnix 배포

- Deployment를 정의하여 Nginx 컨테이너를 담은 pod를 띄운다.
- Pod를 LoadBalancer Type의 Service를 통해 배포한다.
- 자동 생성된 ELB를 통해 Nginx가 배포된 것을 확인한다.


#### 모르는 용어

- EKS(Elastic Kubernetes Service)?
    - Kubernetes Master Plane을 직접 설치하고 운영할 필요 X
    - AWS에서 Kubernetes를 사용할 수 있게 제공하는 관리형 Kubernetes 서비스
- ECS(Elastic Container Service) – 관리형 쿠버네티스 이전, 컨테이너 관리서비스(Fargate-서버리스 or EC2)
- NodeGroup – EKS Master Plane과 통신하며 실제 Pod가 생성되는 Worker Node의 Group Worker Node는 AWS에서 제공하는 Kubernetes 버전 별 최적화 된 AMI를 통해 생성된다.
    - ex) Cluster version – 1.14 → Worker Node는 1.14 최적화 AMI 사용하여 생성
- CloudFormation?
    - Infra as a Code
    - 인프라 관리 간소화, 복제, 변경
        - 템플릿(yaml, json)을 생성하여 인프라를 관리하는 AWS Native Service


- 개발 검수 운영
    - 클러스터 버전 워커노드 버전 0.2 이상 차이나면 알아서 워커노드가 자동 업데이트된다
    - 워커노드 AMI 개발 검수 운영 모두 전부 다시해야
    - 스크립트 자동화 안 해놓으면
    - AMI 스크립트 업데이트
    - 워커노드를 죽였다가 살려야 하므로 저녁에
    - 전부 계획보고서부터 시작

## EKS 환경구성

### [Bastion에 kubectl 설치](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl/)

- [Bation을 kubectl 명령어를 수행하는 workspace로 사용](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl/)

```python
# 1.8버전의 kubectl 다운로드
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.18.0/bin/linux/amd64/kubectl

# 실행 권한 추가
$ chmod +x ./kubectl

# PATH가 설정된 디렉터리로 변경
$ sudo mv ./kubectl /usr/local/bin/kubectl

# 설치 확인
$ kubectl version --client
```

- 리눅스 버전으로 설치
    - 위 명령어로 안 되어서 brew로 설치함

```python
$ brew install kubectl
or
$ brew install kubernetes-cli

# 확인
$ kubectl version --client
```

<img width="464" alt="3" src="https://user-images.githubusercontent.com/48748376/84260170-8aa6a100-ab54-11ea-8a5a-2f6f34e4c861.png">

### IAM에서 EKS 클러스터 역할 생성

- EKS Cluster에 접근하는 권한
- IAM Role 생성
    - 이름 : eksClusterRole
    - 권한 : AmazonEKSClusterPolicy, AmazonEKSServicePolicy

<img width="759" alt="4" src="https://user-images.githubusercontent.com/48748376/84260171-8b3f3780-ab54-11ea-889e-63aa97b15006.png">

### 보안그룹 생성

- EKS 클러스터 보안그룹
    - 이름 : mission-cluster-sg

<img width="423" alt="5" src="https://user-images.githubusercontent.com/48748376/84260173-8bd7ce00-ab54-11ea-8683-2d7e9293fa08.png">

- 클러스터 엔드포인트 액세스
    - 인터넷에서 Kubernetes 클러스터 엔드포인트로의 퍼블릭 액세스를 제한하거나 완전히 비활성화할 수 있다
        - API endpoint acces를 Public으로 설정할 경우 cluster 보안 그룹에 kubectl workspace 인바운드 룰 추가 필요X
    - Amazon EKS는 클러스터와 통신하는 데 사용하는 관리형 Kubernetes API 서버용 엔드포인트를 생성(kubectl과 같은 Kubernetes 관리도구 사용).
        - 기본적으로 이 API 서버 엔드포인트는 인터넷에 공개되어 있으며, API 서버에 대한 액세스는 AWS Identity and Access Management(IAM)와 기본 Kubernetes RBAC(역할 기반 액세스 제어) 를 조합하여 보호한다
- 선택적으로 퍼블릭 엔드포인트에 액세스할 수 있는 CIDR 블록을 제한할 수 있다
    - 특정 CIDR 블록에 대한 액세스를 제한하는 경우에는 프라이빗 엔드포인트도 활성화하거나, 지정한 CIDR 블록에 작업자 노드와 Fargate 포드(사용하는 경우)가 퍼블릭 엔드포인트에 액세스하는 주소가 포함되도록 하는 것이 좋다

### EKS 클러스터 생성

- EKS 클러스터 -> 마스터 플레인
- AWS CLI로 생성
    - 클러스터 구성
        - 이름 : mission-cluster
        - 버전 : 1.14
        - 클러스터 서비스 역할 : iam에서 생성한 역할
            - IAM > 역할 > eksClusterRole 클릭하면 번호 나옴
    - 네트워킹 지정
        - vpc : 1차 과제에서 생성한 vpc
        - 서브넷 : 이전에 생성한 subnet 모두 포함
            - id를 콤마로 구분해서 나열
        - 보안그룹 : mission-cluster-sg
- [공식](https://docs.aws.amazon.com/eks/latest/userguide/create-cluster.html)
    - 서브넷 가용 영역을 최소 2개 이상으로 해야 오류가 안 난다

```python
$ aws eks create-cluster --name mission-cluster[클러스터 이름] \
--kubernetes-version=1.14[버전] \
-–role-arn arn:aws:iam::계정:role/eksClusterRole[클러스터 역할] \
--resources-vpc-config subnetIds=[생성했던 모든 subnet],securityGroupIds=[cluster의 보안그룹] \
--region ap-northeast-2
```

- kubeconfig 파일 생성(kube config update)
    - Nat Instance 접속해서 생성
    - [공식](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/create-kubeconfig.html)

```python
aws eks --region ap-northeast-2 update-kubeconfig --name mission-cluster[클러스터 이름]
```

```python
aws eks --region region-code update-kubeconfig --name cluster_name
```

### NodeGroup 생성

#### CloudFormation으로 Worker Node Group 생성

- CloudFormation > 스택생성
- 템플릿 지정
- 준비된 템플릿 > Amazon S3 URL > https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-05-08/amazon-eks-nodegroup.yaml

<img width="545" alt="6" src="https://user-images.githubusercontent.com/48748376/84260174-8c706480-ab54-11ea-9047-f6287ffc7c46.png">

#### 스택 세부정보 지정

- 스택 이름 : mission-nodegroup-stack
- 파라미터
    - cluster name : mission-cluster clusterConstrolPlaneSecurityGroup : mission-cluster-sg NodeGroupName : mission-wn
- Min : 1
- Desired : 1
- Max : 1

<img width="454" alt="7" src="https://user-images.githubusercontent.com/48748376/84260175-8c706480-ab54-11ea-92d3-766c705df822.png">

- InstanceType : t3.small
- NodeImageSSMParam : 1.14(Cluster와 동일 버전) NodeVolumeSize : 20
- Key : Workernode 접근 키(기존키 이용 또는 새로 생성) [Worker Network Configuration]
- vpcID : mission-vpc
- subnet : private subnet 선택

<img width="454" alt="8" src="https://user-images.githubusercontent.com/48748376/84260178-8da19180-ab54-11ea-8499-a0b7f06ea57f.png">

#### WorkerNode를 클러스터에 조인

- Nat instance에 접속
- AWS IAM Authenticator 구성맵 다운로드

```python
$ wget https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2019-11-15/aws-auth-cm.yaml
```

- kubectl 접근 권한 설정
- aws-auth-cm.yaml 수정
    - CloudFormation으로 생성된 WorkerNode에 부여된 Role을 NodeInstanceRole 값으로 교체 후 저장
    - 홈페이지에서 CloudFormation > 리소스 > NodeInstanceRole 

<img width="671" alt="스크린샷 2020-06-11 오후 5 53 19" src="https://user-images.githubusercontent.com/48748376/84365527-8d64cd00-ac0c-11ea-8010-121572f93beb.png">


- 구성 적용
- Configmap: EKS Master plane에 조인한 워커노드에 권한 설정. IAM 사용자 및 RBAC 엑세스 추가하여 kubectl 명령어 권한 제한 가능

```python
$ kubectl apply -f aws-auth-cm.yaml
```

```python
$ kubectl edit configmap –n kube-system aws-auth
```

- 노드 상태 확인

```python
$ kubectl get node
```

### Nginx deployment 배포

- Nginx-Deployment 생성
    - 띄어쓰기 주의

```python
# filename : nginx-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-nginx
spec:
    selector:
        matchLabels:
            run: my-nginx
    replicas: 2
    template:
        metadata:
            labels:
                run: my-nginx
        spec:
            containers:
            - name: my-nginx
            image: nginx
            ports:
            - containerPort: 80
```

<img width="498" alt="스크린샷 2020-06-11 오후 6 52 29" src="https://user-images.githubusercontent.com/48748376/84371568-d15bd000-ac14-11ea-8dbd-476fded3ac00.png">

- Nginx 파드 배포

```python
$ kubectl apply –f nginx-deployment.yaml //배포
$ kubectl get pod //생성된 파드 확인
```

- Nginx-Service.yaml 생성 (type : loadbalancer)

```python
# filename: nginx-service.yaml

apiVersion: v1
kind: Service
metadata:
    name: my-nginx
    labels:
        run: my-nginx
spec:
    ports:
    - port: 80
      protocol: TCP
    selector:
        run: my-nginx
    type: LoadBalancer
```

<img width="486" alt="스크린샷 2020-06-11 오후 6 52 33" src="https://user-images.githubusercontent.com/48748376/84371578-d456c080-ac14-11ea-9004-84d3155ec947.png">

- Service 배포

```python
$ kubectl apply –f nginx-service.yaml # 배포
$ kubectl get service # 배포된 서비스 확인
# 로드밸런서 타입으로 생성했기 때문에 CLB 타입의 ELB 생성됨
```

```python
# kubectl expose deployment/my-nginx --type=LoadBalancer --port=80"
```

- 웹에서 `EXTERNAL-IP:80`으로 Nginx 접속 확인

##### Link

- [클라우드 코멘토]
