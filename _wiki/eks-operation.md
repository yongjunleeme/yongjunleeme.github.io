---
layout  : wiki
title   : 
summary : 
date    : 2020-06-22 10:59:48 +0900
updated : 2020-06-24 21:20:46 +0900
tags    : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}

## AWS 시스템 운영

### 모니터링 체크리스트 항목

- 항목의 대분류, 중분류, 소분류, 정산기준, 확인방법

| 대분류 | 중분류 | 설명 |
| 비용 최적화 | Amazon EC2 예약 인스턴스 최적화|Amazon Elastic Compute Cloud(EC2) 컴퓨팅 사용 기록을 점검하고 최적의 부분 선결제 예약 인스턴스 수를 계산합니다. 모든 통합 결제 계정을 망라하여 집계한 지난달의 시간별 사용량을 기반으로 권장 사항을 제안합니다. 권장 사항의 계산 방법에 대한 자세한 내용은 Trusted Advisor FAQ의 ‘예약 인스턴스 최적화 점검 질문’을 참조하십시오. 예약 인스턴스를 사용하면 저렴한 일회성 요금을 지불하고 해당 인스턴스에 대해 시간당 요금을 대폭 할인받을 수 있습니다. 결제 시스템은 사용자의 비용을 최소화하기 위해 자동으로 예약 인스턴스 요금을 우선 적용합니다.|
| 비용 최적화 | 사용률이 낮은 Amazone EC2 인스턴스 | 지난 14일 동안 실행된 적이 있는 Amazon Elastic Compute Cloud(EC2) 인스턴스를 점검하여, 4일 이상 일일 CPU 사용률이 10% 이하이고 네트워크 I/O가 5MB 이하였던 경우 사용자에게 알려줍니다. 실행되는 인스턴스에 대해 시간당 사용 요금이 발생합니다. 사용률이 낮도록 설계된 시나리오가 있을 수도 있지만 인스턴스의 수와 크기를 관리하여 비용을 줄일 수 있는 경우가 많습니다. 온디맨드 인스턴스의 현재 사용률과 인스턴스의 사용률이 낮을 수 있는 것으로 예상되는 일수를 사용하여 예상 월별 비용 절감액을 계산할 수 있습니다. 예약 인스턴스나 스팟 인스턴스를 사용하는 경우 또는 인스턴스가 종일 실행되지 않는 경우에는 실제 절감액이 달라집니다. 일일 사용률 데이터를 보려면 이 점검 항목에 대한 보고서를 다운로드하십시오. |
| 비용 최적화 |유휴 상태의 Load Balancer|활발하게 사용되지 않는 로드 밸런서에 대한 Elastic Load Balancing 구성을 점검합니다. 구성된 모든 로드 밸런서에서 요금이 발생합니다. 로드 밸런서에 연결된 백엔드 인스턴스가 없거나 네트워크 트래픽이 매우 제한적인 경우에는 로드 밸런서가 효율적으로 사용되지 않습니다.|
| 보안 | 보안 그룹 - 제한ㅎ없는 특정 포트(무료)| 특정 포트에 대한 제한 없는 액세스(0.0.0.0/0)를 허용하는 규칙에 대해 보안 그룹을 점검합니다. 제한 없는 액세스는 악의적인 활동(해킹, 서비스 거부 공격, 데이터 손실)의 가능성을 높입니다. 위험성이 가장 높은 포트에는 빨간색 플래그가 지정되고 덜 위험한 포트에는 노란색 플래그가 지정됩니다. 녹색 플래그가 지정된 포트는 일반적으로 HTTP 및 SMTP와 같이 제한 없는 액세스가 필요한 애플리케이션에서 사용합니다. 의도적으로 이런 방식에 따라 보안 그룹을 구성한 경우 추가적인 보안 조치를 사용하여 IP 테이블 등의 인프라를 보호하는 것이 좋습니다.|
| 보안 | 보안 그룹 - 제한없는 엑세스 | 리소스에 제한 없는 액세스를 허용하는 규칙에 대해 보안 그룹을 점검합니다. 제한 없는 액세스는 악의적인 활동(해킹, 서비스 거부 공격, 데이터 손실)의 가능성을 높입니다.|
| 보안 | IAM사용(무료) | AWS Identity and Access Management(IAM)의 사용을 점검합니다. IAM을 사용하여 AWS의 사용자, 그룹, 역할을 생성할 수 있고 권한을 사용하여 AWS 리소스에 대한 액세스를 제어할 수 있습니다.|
| 내결함성 | EBS 스냅샷 | 사용 가능하거나 사용 중인 Amazon Elastic Block Store(EBS) 볼륨에 대한 스냅샷의 수명을 점검합니다. Amazon EBS 볼륨은 복제되더라도 장애가 발생할 수 있습니다. 스냅샷은 특정 시점으로 복구 및 내구력 있는 스토리지를 위해 Amazon Simple Storage Service(S3)에 보관됩니다.|
| 내결함성 | EC2 가용 영역 밸런싱 | 리전 내 가용 영역 전반에 걸쳐 Amazon Elastic Compute Cloud(EC2) 인스턴스의 분산을 점검합니다. 가용 영역은 다른 가용 영역에서 발생한 장애의 영향을 받지 않으며, 동일 리전 내의 다른 가용 영역에 대한 저렴하고 지연 시간이 짧은 네트워크 연결을 제공하도록 설계된 개별 위치입니다. 동일 리전의 여러 가용 영역에서 인스턴스를 시작함으로써 단일 장애 지점으로부터 애플리케이션을 보호할 수 있습니다. |
| 내결함성 | Load Balancer | Load Balancer 구성을 점검합니다. Elastic Load Balancing을 사용할 때 Amazon Elastic Compute Cloud(EC2)의 내결함성 수준을 높이려면 한 리전의 여러 가용 영역에서 각각 동일한 수의 인스턴스를 실행하는 것이 좋습니다. 구성된 로드 밸런서에는 요금이 발생하므로, 이는 비용 최적화 점검이기도 합니다. |
| 성능 | 높은 사용률의 Ec2 인스턴스 | 지난 14일 동안 실행된 적이 있는 Amazon Elastic Compute Cloud(EC2) 인스턴스를 점검하고, 4일 이상 일일 CPU 사용률이 90%를 넘은 경우 알립니다. 사용률이 일관되게 높다는 것은 성능이 최적화되어 있고 안정적임을 나타낼 수 있지만 애플리케이션의 리소스가 충분하지 않다는 의미일 수도 있습니다. 일일 CPU 사용률 데이터를 보려면 이 점검 항목에 대한 보고서를 다운로드하십시오.|
| 성능 | EBS 프로비저닝된 IOPS(SSD) 볼륨 연결 구성 | Amazon EBS 최적화 인스턴스가 아닌 Amazon Elastic Compute Cloud(EC2) 인스턴스에 연결된 프로비저닝된 IOPS(SSD) 볼륨이 있는지 점검합니다. Amazon Elastic Block Store(EBS)의 프로비저닝된 IOPS 볼륨은 EBS 최적화 인스턴스에 연결된 경우에만 기대 성능을 발휘할 수 있도록 설계되었습니다.|
| 성능 | EC2 보안 그룹에 있는 많은 수의 규칙 | 과도한 수의 규칙이 있는지 각 Amazon Elastic Compute Cloud(EC2) 보안 그룹을 점검합니다. 보안 그룹에 많은 수의 규칙이 있는 경우 성능이 저하될 수 있습니다.|

- 참고 : https://aws.amazon.com/ko/premiumsupport/technology/trusted-advisor/best-practice-checklist/
- 정산기준 : https://aws.amazon.com/ko/blogs/korea/check-it-out-new-aws-pricing-calculator-for-ec2-and-ebs/ 
- 확인방법 : https://aws.amazon.com/ko/premiumsupport/technology/trusted-advisor/best-practice-checklist/ 

#### 백업 복구 정책(EC2- lifeCycleManager(volume backup),AMI 등)

참조링크
- https://dev.classmethod.jp/articles/ec2-instance-backup-by-amazon-dlm/
- https://aws.amazon.com/ko/premiumsupport/knowledge-center/ebs-snapshot-data-lifecycle-manager/

#### AWS Native 서비스를 이용한 모니터링(CloudWatch Dashboard 구성, CloudTrail, Config, vpc flowlogs 등)

- http://labs.brandi.co.kr/2019/05/30/kwakjs.html

#### 클라우드 모니터링 솔루션 제안(Whatap kube(상용 서비스), Grafana+Prometheus 자체 구축, data dog 등)





#### [EKS Cloudwatch 연동](https://aws-diary.tistory.com/57?category=753092)


- [Amazon EKS에서 Container Insights 빠른 시작 설정](https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html)
- [클러스터 지표를 수집하도록 CloudWatch 에이전트 설정](https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-metrics.html)
- [DaemonSet으로 FluentD를 설정하여 CloudWatch Logs에 로그 전송](https://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs.html)

