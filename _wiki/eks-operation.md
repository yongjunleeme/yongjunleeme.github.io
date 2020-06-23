---
layout  : wiki
title   : 
summary : 
date    : 2020-06-22 10:59:48 +0900
updated : 2020-06-23 19:11:11 +0900
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
| 비용 최적화 | Amazon EC2 예약 인스턴스 최적화<br/>사용률이 낮은 Amazon EC2 인스턴스<br/>유휴 상태의 Load Balancer|Amazon Elastic Compute Cloud(EC2) 컴퓨팅 사용 기록을 점검하고 최적의 부분 선결제 예약 인스턴스 수를 계산합니다. 모든 통합 결제 계정을 망라하여 집계한 지난달의 시간별 사용량을 기반으로 권장 사항을 제안합니다. 권장 사항의 계산 방법에 대한 자세한 내용은 Trusted Advisor FAQ의 ‘예약 인스턴스 최적화 점검 질문’을 참조하십시오. 예약 인스턴스를 사용하면 저렴한 일회성 요금을 지불하고 해당 인스턴스에 대해 시간당 요금을 대폭 할인받을 수 있습니다. 결제 시스템은 사용자의 비용을 최소화하기 위해 자동으로 예약 인스턴스 요금을 우선 적용합니다.<br/>지난 14일 동안 실행된 적이 있는 Amazon Elastic Compute Cloud(EC2) 인스턴스를 점검하여, 4일 이상 일일 CPU 사용률이 10% 이하이고 네트워크 I/O가 5MB 이하였던 경우 사용자에게 알려줍니다. 실행되는 인스턴스에 대해 시간당 사용 요금이 발생합니다. 사용률이 낮도록 설계된 시나리오가 있을 수도 있지만 인스턴스의 수와 크기를 관리하여 비용을 줄일 수 있는 경우가 많습니다. 온디맨드 인스턴스의 현재 사용률과 인스턴스의 사용률이 낮을 수 있는 것으로 예상되는 일수를 사용하여 예상 월별 비용 절감액을 계산할 수 있습니다. 예약 인스턴스나 스팟 인스턴스를 사용하는 경우 또는 인스턴스가 종일 실행되지 않는 경우에는 실제 절감액이 달라집니다. 일일 사용률 데이터를 보려면 이 점검 항목에 대한 보고서를 다운로드하십시오.<br/>활발하게 사용되지 않는 로드 밸런서에 대한 Elastic Load Balancing 구성을 점검합니다. 구성된 모든 로드 밸런서에서 요금이 발생합니다. 로드 밸런서에 연결된 백엔드 인스턴스가 없거나 네트워크 트래픽이 매우 제한적인 경우에는 로드 밸런서가 효율적으로 사용되지 않습니다.


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

