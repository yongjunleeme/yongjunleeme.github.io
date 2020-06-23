---
layout  : wiki
title   : 
summary : 
date    : 2020-06-22 10:59:48 +0900
updated : 2020-06-23 18:51:00 +0900
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

| 대분류      | 중분류                                                                                        | 정산기준 | 확인방법                                                                                     |
| 비용 최적화 | Amazon EC2 예약 인스턴스 최적화\n사용률이 낮은 Amazon EC2 인스턴스\n유휴 상태의 Load Balancer | aaa      | https://aws.amazon.com/ko/premiumsupport/technology/trusted-advisor/best-practice-checklist/ |

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

