---
layout: post
title:  "High Availability In AWS"
date:   2015-01-01 08:00:00
categories: InfraArchitecture
---
* TOC
{:toc}

# Load Balancer
## ELB
  * Elastic Load Balancing

### Performance
  * ELB 의 처리용량을 초과하는 트랜젝션이 발생할 경우 ELB 는 auto scale up을 진행함
    - 하지만 auto scale up을 하는 과정에서 성능저하 발생
    - auto scale up 성능저하를 막기 위해서는 Pre-Warn 기능을 사용해야함
    - pre-Warn 기능을 사용하기 위해선 AWS Support 서비스를 이용해야만함
  * Connetion timeout 은 60 sec 로 초기화 되어있음
    - AWS Support 를 통해서 17분까지 조정 가능

### Price
  * AWS Support : $49/month for developer, over $100/month for bussiness, over $15,000/month for enterprise
    - ELB 를 설정하기 위해서 필요
    - 어떤 Support를 이용해야하는지는 잘 모르겠음
  * ELB : $0.025/hour + $0.008/GB

#### Reference
  * [AWS Support 가격 & 기능](https://aws.amazon.com/ko/premiumsupport/)
  * [AWS ELB 사용사례](http://www.slideshare.net/awskorea/gaming-on-aws-aws-dev-test)
  * [AWS ELB 가격](http://aws.amazon.com/ko/elasticloadbalancing/pricing/)

## HAProxy With Keepalived
#### Performance
  * 1 vCPU + 1GB memory로 10~ 100k HTTP reqeust/sec 처리
    - ulimit, conntract 등의 설정이 필요
  * 1대의 HAProxy 처리용량을 초과하는 트래픽이 발생하면 DNS roundrobin 을 함께 활용하는 방법이 있음
    - Elastic IP 한개 더 필요

### Price
  * Elasctic IP 1개
    - Keepalived 를 위한 virtual IP필요
    - EC2와 연결된 EIP는 무료지만 임시로 발급중인 EIP는 비용을 받음. 즉, VIP는 유료.
  * EC2 2대
    - Haproxy 와 Keepalived 를 설치할 서버 2대 ( active, stand-by )
    - t2.micro(1 vCPU, 1GB memory ) 1년 계약시 $126/year

### Reference
  * [AWS EC2 가격](http://aws.amazon.com/ko/ec2/pricing/)
  * [HAProxy 사용사례](http://www.slideshare.net/reinkim/ndc14)

## ELB vs HAProxy In AWS
### Maintenance
  * ELB 는 logging, monitoring 에 대한 기능이 없으므로 AWS CloudWatch 를 이용해서 분석해야함

### Price
  * AWS Support 를 developer 버전으로 사용가능하다면 HAProxy에비해 비용차원에서 월등히 유리함

#### Reference
  * [Comparison Analysis:Amazon ELB vs HAProxy EC2](http://harish11g.blogspot.in/2012/11/amazon-elb-vs-haproxy-ec2-analysis.html)

# Auto-Scaling

#### Reference
  * [AWS Auto-Scaling 사용법](https://opentutorials.org/course/608/3302)