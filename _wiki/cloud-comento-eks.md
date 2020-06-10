---
layout  : wiki
title   : 
summary : 
date    : 2020-06-10 18:21:56 +0900
updated : 2020-06-10 19:53:25 +0900
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

### Kubernetes

- 컨테이너 오케스트레이션 툴(Docker Swarm 같은..)
- 컨테이너를 쉽고 빠르게 배포/확장하고 관리를 자동화해주는 오픈소스 플랫폼 = 컨테이너 관리 툴
    - 블루그린 배포, 카나리 배포 등을 실현할 수 있도록 Deployment, Daemonset같은 다양한 배포 방식을 지원하고 Auto Scaling 등을 지원

#### 쿠버네티스 클러스터 아키텍처

1

- 여러 대의 서버가 하나의 클러스터로 연결
- 쿠버네티스 마스터 -> 컨트롤 플레인이 실행됨, 클러스터의 두뇌
    - 컨테이너 스케줄링, 서비스 관리, API 요청 수행 (파드, 리소스 컨트롤러, 로드밸런서 관리)
- 쿠버네티스 워커노드 -> 사용자의 워크로드 실행 마스터의 관리 아래 실제 pod같은
리소스가 생성되는 노드

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
- Pod는 각각의 WorkerNode에 배포되고 서비스를 통해 외부에 노출된다. (사람들이 인터넷을 통해 접근할 수 있게 외부로 노출)

2

#### Ngnix 배포

- Deployment를 정의하여 Nginx 컨테이너를 담은 pod를 띄운다.
- Pod를 LoadBalancer Type의 Service를 통해 배포한다.
- 자동 생성된 ELB를 통해 Nginx가 배포된 것을 확인한다.


## EKS 환경구성

### [Bastion에 kubectl 설치](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl/)

- 리눅스 버전으로 설치
    - 안 되어서 brew로 설치함

```python
$ brew install kubectl
or
$ brew install kubernetes-cli

# 확인
$ kubectl version --client
```

3

### IAM에서 EKS 클러스터 역할 생성

- EKS Cluster에 접근하는 권한
- IAM Role 생성
    - 이름 : eksClusterRole
    - 권한 : AmazonEKSClusterPolicy, AmazonEKSServicePolicy

4

### 보안그룹 생성

- EKS 클러스터 보안그룹
    - 이름 : mission-cluster-sg

5

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
        - 클러스터 서비스 역할 : iam에서 생성한 역할 부여
    - 네트워킹 지정
        - vpc : 1차 과제에서 생성한 vpc
        - 서브넷 : 이전에 생성한 subnet 모두 포함
        - 보안그룹 : mission-cluster-sg

```python
$ aws eks create-cluster --name mission-cluste[클러스터 이름] --kubernetes-version=1.14[버전] –role-arn arn:aws:iam::계정:role/eksClusterRole[클러스터 역할] --resources-vpc-config subnetIds=[생성했던 모든 subnet],securityGroupIds=[cluster의 보안그룹] --region ap-northeast-2
```

- kubeconfig 파일 생성(kube config update)

```python
aws eks --region ap-northeast-2 update-kubeconfig --name mission-cluster[클러스터 이름]
```

### NodeGroup 생성

#### CloudFormation으로 Worker Node Group 생성

- CloudFormation > 스택생성
- 템플릿 지정
- 준비된 템플릿 > Amazon S3 URL > https://amazon-eks.s3.us-west-2.amazonaws.com/cloudformation/2020-05-08/amazon-eks-nodegroup.yaml

6

#### 스택 세부정보 지정

- 스택 이름 : mission-nodegroup-stack
- 파라미터
    - cluster name : mission-cluster clusterConstrolPlaneSecurityGroup : mission-cluster-sg NodeGroupName : mission-wn
- Min : 1
- Desired : 1
- Max : 1

7

- InstanceType : t3.small
- NodeImageSSMParam : 1.14(Cluster와 동일 버전) NodeVolumeSize : 20
- Key : Workernode 접근 키(기존키 이용 또는 새로 생성) [Worker Network Configuration]
- vpcID : mission-vpc
- subnet : private subnet 선택

8

#### WorkerNode를 클러스터에 조인

- AWS IAM Authenticator 구성맵 다운로드

```python
$ wget https://amazon-eks.s3.us-west-2.amazonaws.com/ cloudformation/2019-11-15/aws-auth-cm.yaml
```

- aws-auth-cm.yaml 수정
    - CloudFormation으로 생성된 WorkerNode에 부여된 Role을 NodeInstanceRole 값으로 교체 후 저장

- 구성 적용

```python
$ kubectl apply -f aws-auth-cm.yaml
```

- 노드 상태 확인

```python
$ kubectl get node
```

9

### Nginx deployment 배포

- Nginx-Deployment 생성

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

- Service 배포

```python
$ kubectl apply –f nginx-service.yaml # 배포
$ kubectl get service # 배포된 서비스 확인
# 로드밸런서 타입으로 생성했기 때문에 CLB 타입의 ELB 생성됨
```

- 웹에서 `EXTERNAL-IP:80`으로 Nginx 접속 확인

##### Link

- [클라우드 코멘토]
