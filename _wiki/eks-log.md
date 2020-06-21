---
layout  : wiki
title   : 
summary : 
date    : 2020-06-13 11:13:11 +0900
updated : 2020-06-17 18:04:02 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## Background

- 컨테이너 환경에서의 로그 수집을 위해 EFK(ElastiSearch + Fluentd + Kibana) Stack을 구축
- 로그 저장소인 ElastiSearch, 로그 수집기인 Fluentd, 로그 시각화 툴인 Kibana를 EKS 환경에서 구축
    - ElastiSearch 구축 
    - Fluentd 구축
    - Kibana 구축
- 이전 버전까지 진행된 상태에서 진행(EKS 클러스터, NodeGroup의 WorkerNode(Instance type은 t3.medium으로 생 성해야 out of memory 발생 X) 생성, Nginx 배포)

### EFK?

- ElasticSearch + Fluentd + Kibana
- 쿠버네티스는 파드가 정상상태가 아니면 새로 생성 
    - 죽은 파드에 있는 컨테이너가 남긴 로그는 어디로..?
- 컨테이너의 로그를 로그 저장소에 수집 
    - 죽은 컨테이너의 로그도 남는다
- Fluentd
    - 컨테이너의 스트림 로그를 수집하는 로그 수집기. 모든 노드마다 동일하게 배포되어야함 → Daemonset
- ElasticSearch – 로그를 저장하기 위한 대용량 저장소
- Kibana - ElasticSearch와 연동하여 로그 시각화
    - 로그 시각화를 통한 문제 해결 및 예방 가능

<img width="724" alt="1" src="https://user-images.githubusercontent.com/48748376/84875224-a5cb6080-b0c0-11ea-81ba-63ee958ec99d.png">

### Daemonset?

- 쿠버네티스 컨트롤러(Deployment, ReplicaSet 등) 중 하나
    - 컨트롤러란? 쿠버네티스 기본 object를 생성하고 관리하는 역할
- Daemonset은 Pod가 각각의 노드에 하나씩만 배포되게 하는 Pod 관리 컨트롤러
    - ex) 모든 노드에 로그 수집용 Daemonset pod를 띄움

## ELK Stack 구축을 통한 로그 수집(실행)

### 노드그룹 생성

- 이름 : mission-wn
- 노드 IAM role : WorkerNodeInstanceRole
- 서브넷 : 이전에 생성한 private 서브넷
- 키페어 : Nat instanc의 키페어 또는 Worker node 전용 키페어 생성하여 사용 (컴퓨팅 구성 설정 비용 때문에)
- AMI : Amazon Linux2
- 인스턴스 유형 : t3.medium(t3.medium 이하는 memory 부족함)
- 디스크 크기 : 4GB
- 조정 구성 설정
    - 최소 : 1 최대 1 원하는 크기 1(비용 때문에)

### ElasticSearch란?

- 텍스트, 숫자, 위치기반정보, 정형 및 비정형 데이터 등 모든 유형의 데이터를 위한 분산형 오픈소스검색 및 분석엔진
    - 많은 양의 데이터를 보관하고 실시간으로 분석하는 엔진
- ElasticSearch를 설치하려면..?
    - java 8 설치(ElasticSearch는 jvm 위에서 돌기 때문) > ElasticSearch install > service 재시작 ......
    - But Kubernetes로 시작하면?
        - `elasticSearch.yaml`로 elasticSearch 배포 > NodePortType으로 배포
        - `ElasticSearch yaml` 파일 작성 > `kubectl apply`로 배포하면 끝
- ElasticSearch를 NodePort type의 서비스로 배포하여 2주차 때 생성한 Nginx의 LoadBalancer에 연결하여 통신 확인

### ElasticSearch 배포

- elasticSearch.yaml로 elasticSearch 배포 > NodePortType으로 배포

```python
## filename: elasticSearch.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: elasticsearch
  labels:
    app: elasticsearch
spec:
  replicas: 1
  selector:
    matchLabels:
      app: elasticsearch
  template:
    metadata:
      labels:
        app: elasticsearch
    spec:
      containers:
      - name: elasticsearch
        image: elastic/elasticsearch:6.4.0
        env:
        - name: discovery.type
          value: "single-node"
        ports:
        - containerPort: 9200
        - containerPort: 9300
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: elasticsearch
  name: elasticsearch-svc
  namespace: default
spec:
  ports:
  - name: elasticsearch-rest
    nodePort: 30920 // Node의 Port port: 9200 // Service의 Port
    protocol: TCP
    targetPort: 9200 // Pod의 Port
```

```python
# 배포
$ kubectl apply –f elasticSearch.yaml 

# 배포된 서비스 확인
$ kubectl get service 
```

#### Loadbalancer 리스너에 추가

- 리스너란?
    - 리스너는 정의한 프로토콜과 포트를 사용하여 연결요청을 확인하는 프로세스.
    - 리스너에 정의한 규칙에 따라 로드밸런서가 등록된 대상으로 요청을 라우팅하는 방법 결정
- [EC2] → [로드밸런서] → [Listeners]탭 클릭 → [Edit]

<img width="920" alt="2" src="https://user-images.githubusercontent.com/48748376/84875234-a95ee780-b0c0-11ea-81a8-8dc5ab3975b8.png">

#### LoadBalancer의 9200 포트로 외부 접근 허용 위해 Loadbalancer security group inbound rule 추가

- [EC2] → [로드밸런서] → [Security]탭 클릭 → Security Group ID 클릭 → security group 화면에서 inbound rule 편집

<img width="941" alt="3" src="https://user-images.githubusercontent.com/48748376/84875235-a9f77e00-b0c0-11ea-940e-c1d30e719351.png">

<img width="1404" alt="4" src="https://user-images.githubusercontent.com/48748376/84875237-aa901480-b0c0-11ea-8dd5-a20fdbde5403.png">

### Kibana

- Elastic Stack을 기반으로 구축된 오픈 소스 프론트엔드 애플리케이션으로, Elasticsearch에서 색인된 데이터를 검색하고 시각화하는 기능을 제공
    - 오픈 소스 기반의 분석 및 시각화 플랫폼
- Kibana 배포
    - kibana.yaml로 kibana 배포 > NodePortType으로 배포
    - kibana를 NodePort type의 서비스로 배포하여 2주차 때 생성한 Nginx의 LoadBalancer에 연결하여 외부 접근

#### Kibana 배포

- `kibana.yaml`로 kibana 배포 > NodePortType으로 배포

```python
## filename: kibana.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: kibana
  labels:
    app: kibana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: kibana
  template:
    metadata:
      labels:
        app: kibana
    spec:
      containers:
      - name: kibana
        image: elastic/kibana:6.4.0
        env:
        - name: SERVER_NAME
        value: "kibana.kubenetes.example.com"
        - name: ELASTICSEARCH_URL
        value: "http://elasticsearch- svc.default.svc.cluster.local:9200"
        ports:
        - containerPort: 5601
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: kibana
```


```python
# 배포
$ kubectl apply –f kibana.yaml 

# 배포된 서비스 확인
$ kubectl get service
```

#### Loadbalancer 리스너에 추가

- [EC2] → [로드밸런서] → [Listeners]탭 클릭 → [Edit]

<img width="1072" alt="5" src="https://user-images.githubusercontent.com/48748376/84875239-ab28ab00-b0c0-11ea-9be4-477c97281e23.png">

#### LoadBalancer의 5601 포트로 외부 접근 허용 위해 Loadbalancer security group inbound rule 추가

- [EC2] → [로드밸런서] → [Security]탭 클릭 → Security Group ID 클릭 → security group 화면에서 inbound rule 편집

<img width="952" alt="6" src="https://user-images.githubusercontent.com/48748376/84878480-db724880-b0c4-11ea-93d5-e92c073d70a0.png">


- Kibana web 접근 확인
    - LB DNS:5601

<img width="1560" alt="7" src="https://user-images.githubusercontent.com/48748376/84875241-ac59d800-b0c0-11ea-92af-5065e97b1a56.png">

### Fluentd

- Fluentd란?
    - 로그 수집기
    - 다양한 데이터소스(HTTP, TCP 등)로부터 원하는 형태로 가공되어 여러 목적지(EasticSearch, s3) 등으로 전달 가능
- `luentd.yaml`로 fluentd Daemonset pod 배포

```python
## filename: fluentd.yaml

---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: fluentd
  namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: fluentd
  namespace: kube-system
rules:
- apiGroups:
  - ""
  resources:
  - pods
  - namespaces
  verbs:
  - get
  - list
  - watch

---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: fluentd
roleRef:
  kind: ClusterRole
  name: fluentd
  apiGroup: rbac.authorization.k8s.io
subjects:
```

```python
# 배포
$ kubectl apply –f fluentd.yaml 

# 배포된 서비스 확인
$ kubectl get service
```

### Kibana로 로그 시각화

- Kibana 인덱스 패턴 생성
    - 인덱스 패턴 생성
        - [Management] → [Index Patterns] → Index pattern : logstash-*→ next step → @timestamp→ create index pattern
- 로그 시각화
    - 검색필터를 설정하여 nginx의 로그 확인
        - [Discover] → 검색필터 설정 → kuberntes.labels.run is my-nginx(nginx 배포 때 사용 한 Label)

## Link

- [클라우드 코멘토]
