---
layout: post
title: "Terraform, Helm을 이용한 AWS EKS 구성"
date: 2020-08-28 00:00:00
lastmod: 2022-04-07 00:00:00
categories: BackEnd
tags: BackEnd Terraform, Helm, Kubernetes, AWS
---

![Terraform & AWS Logo](https://avatars0.githubusercontent.com/u/31414033?s=200&v=4){:class="imgTitle"}  
(이미지 출처 : [https://github.com/terraform-aws-modules](https://github.com/terraform-aws-modules))  

Terraform을 이용해서 AWS EKS를 구성하고 Helm을 이용해서 ingress와 application을 EKS 위에 띄우는 내용을 살펴봅니다.  
진행방식은 AWS에서 제공한 [from-zero-to-eks-with-terraform-and-helm](https://aws.amazon.com/ko/blogs/startups/from-zero-to-eks-with-terraform-and-helm) 라는 가이드 문서의 예제를 따라하는 방식으로 진행합니다.  

<!--more-->

참고로,  
AWS의 가이드 문서는 Helm 2.x를 사용했지만 이 글에서는 Helm 3.x를 사용했기 때문에 약간 내용의 차이가 있습니다.  

그리고 예제로 사용되는 [샘플 코드](https://github.com/terraform-providers/terraform-provider-aws/tree/master/examples/eks-getting-started)는 Terraform에서 공식적으로 제공하는 정보입니다.  

이 글에서 사용된 예제와는 관계 없지만 [terraform-aws-modules](https://github.com/terraform-aws-modules/terraform-aws-eks)에서도 Terraform으로 AWS EKS를 구성 방법에 대한 정보를 많이 얻을 수 있습니다.  

# 작업환경 

Ubuntu 18.04 LTS 환경에서 진행했습니다. 진행하기에 앞서 사전에 아래와 같은 툴 설치와 설정이 필요합니다.  
그 준비작업에 대해서는 이 글 하단의 [준비작업](# 준비작업)을 참고해주시기 바랍니다.  

  * Git
  * Terraform
  * Kubectl
  * AWS-IAM-AUTHENICATOR
  * Helm
  * AWS 계정설정


# EKS 생성

## 샘플 Terraform 스크립트 준비

~~~terminal
$ cd ~
$ git clone https://github.com/terraform-providers/terraform-provider-aws.git
$ cd terraform-provider-aws/examples/eks-getting-started
~~~

디렉토리의 파일들을 확인해보면 ```.tf``` 확장자 파일들이 존재하는 것을 확인할 수 있습니다. 이 파일들이 terraform 스크립트이며 샘플 EKS 클러스터를 구성하도록 작성되어있습니다.  

~~~
$ ll

eks-cluster.tf  
eks-worker-nodes.tf  
outputs.tf  
providers.tf  
README.md  
variables.tf  
vpc.tf  
workstation-external-ip.tf
~~~


```variables.tf``` 파일을 살펴보면 aws_region의 default 값이 ```us-west-2```로 되어있을 것입니다. 원하시는 region으로 변경하시면 됩니다. 저는 서울리전(```ap-northeast-2```)으로 변경했습니다. 마찬가지로 cluster-name도 원하시면 변경하시면 됩니다.  

~~~terminal
$ cat variables.tf

variable "aws_region" {
  default = "ap-northeast-2"
}

variable "cluster-name" {
  default = "terraform-eks-demo"
  type    = string
}
~~~


사실 Terraform을 사용하기 위해서는 ```.tf``` 파일들을 작성하는 방법과 AWS 컴포넌트들의 정보에 대해 많은 이해가 필요합니다.  
하지만 이 예제는 EKS를 구성하는 것을 목적으로하고 EKS가 구성되고 나면 그 위에 application을 띄우고 application 간의 관계를 만들어가는 것은 Kubernetes에 대한 이해만으로도 진행이 가능합니다.  

즉, 이 글에서 다루는 수준의 Terraform 지식만 가지고 있어도 연습용으로 만든 서비스들을 Kubernetes 환경에 배포해서 테스트 해보는 것이 가능해집니다.  

## EKS 생성

### Terraform Initialization

Terraform을 사용하기 위해 초기화를 하는 작업입니다.  

~~~terminal
$ terraform init
~~~

### Terraform Plan

```.tf``` 파일의 내용을 실제로 적용 가능한지 확인하는 작업입니다.  
  
~~~terminal 
$ terraform plan
~~~

### Terraform Apply

```.tf``` 파일의 내용대로 리소스를 생성, 수정, 삭제하는 작업입니다.  
현재는 최초로 실행하기 때문에 리소스를 생성만 하게 됩니다.  

~~~terminal
$ terraform apply
~~~

```apply```가 실행되기 전에 진행여부에 대해서 확인질문 과정을 거칩니다.  
아래와 같은 문구가 나오면 ```yes```라고 입력 후 엔터를 치시면 됩니다.  

~~~terminal
Do you want to perform these actions?
Terraform will perform the actions described above.
Only 'yes' will be accepted to approve.

Enter a value: 
~~~


<!--ads-->

# Kubectl 설정

EKS 생성이 완료되었으면 kubectl을 이용해서 EKS에 접근을 해야합니다. 아래 명령어를 통해 접근할 EKS의 정보를 yaml 형태의 kubectl을 yaml config 로 출력할 수 있습니다.  

~~~terminal
$ terraform output kubeconfig
~~~

그 정보를 kubectl의 설정파일에 입력하시면 됩니다.  

~~~terminal
$ mkdir ~/.kube/
$ terraform output kubeconfig > ~/.kube/config
~~~

확인 작업을 위해 kubectl 버전을 확인해서 Client, Server의 버전 정보가 잘 출력되는 것을 확인합니다.  

~~~teminal
$ kubectl version

Client Version: version.Info{Major:"1", Minor:"19", GitVersion:"v1.19.0", GitCommit:"e19964183377d0ec2052d1f1fa930c4d7575bd50", GitTreeState:"clean", BuildDate:"2020-08-26T14:30:33Z", GoVersion:"go1.15", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"17+", GitVersion:"v1.17.9-eks-4c6976", GitCommit:"4c6976793196d70bc5cd29d56ce5440c9473648e", GitTreeState:"clean", BuildDate:"2020-07-17T18:46:04Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
~~~


## ConfigMap 접근 설정

Kubenetes Master 노드의 ConfigMap에 대해 EKS 클러스터가 접근할 수 있도록 설정을 합니다.  

~~~terminal
$ terraform output config_map_aws_auth > configmap.yml
$ kubectl apply -f configmap.yml
~~~

에러메시지 없이 정상적으로 kubectl 명령어를 통해 EKS의 kubernetes 설정이 변경되는 것을 확인했습니다.  


# EKS 재배포

## Worker Node 개수 변경

먼저 현재 worker node의 개수를 확인합니다. 저의 경우에는 아래와 같이 1개의 worker 노드가 READY 상태입니다.  

~~~teminal
$ kubectl get nodes -o wide

NAME                                            STATUS   ROLES    AGE   VERSION              INTERNAL-IP   EXTERNAL-IP    OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-10-0-1-172.ap-northeast-2.compute.internal   Ready    <none>   48m   v1.17.9-eks-4c6976   10.0.1.172    15.165.7.203   Amazon Linux 2   4.14.186-146.268.amzn2.x86_64   docker://19.3.6
~~~

```eks-worker-nodes.tf``` 파일을 확인해보면 ```desired_size```, ```max_size```, ```min_size```가 1로 설정된 것을 볼 수 있습니다.  

~~~terminal
$ cat eks-worker-nodes.tf 

...(생략)

  scaling_config {
    desired_size = 1
    max_size     = 1
    min_size     = 1
  }

...(생략)
~~~

그 값을 아래와 같이 수정하고 다시 Terraform 배포를 진행해보겠습니다.  

~~~terminal

  scaling_config {
    desired_size = 2
    max_size     = 10
    min_size     = 2
  }

~~~

배포를 하기 전에 plan 명령어를 수행합니다.  

~~~terminal 
$ terraform plan


An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_eks_node_group.demo will be updated in-place

...(생략)
      ~ scaling_config {
          ~ desired_size = 1 -> 2
          ~ max_size     = 1 -> 10
          ~ min_size     = 1 -> 2
        }
...(생략)
`
Plan: 0 to add, 1 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.

~~~

기존에 비해 1개의 영역이 수정되었다는 메시지와 수정 사항에 대해서 출력해주는 것을 확인할 수 있습니다.  

이제 배포를 해보고 다시 워커 노드의 개수를 확인해봅니다.  
배포시에 진행여부에 대해서 확인질문에는 ```yes```로 답하시면 됩니다.  

~~~terminal 
$ terraform apply

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: 
~~~

워커 노드의 개수를 확인해보면 1개의 worker node가 추가된 것을 확인하실 수 있습니다.  


~~~terminal
$ kubectl get nodes -o wide

NAME                                            STATUS     ROLES    AGE   VERSION              INTERNAL-IP   EXTERNAL-IP    OS-IMAGE         KERNEL-VERSION                  CONTAINER-RUNTIME
ip-10-0-0-72.ap-northeast-2.compute.internal    Ready   <none>   19s   v1.17.9-eks-4c6976   10.0.0.72     3.35.0.241     Amazon Linux 2   4.14.186-146.268.amzn2.x86_64   docker://19.3.6
ip-10-0-1-172.ap-northeast-2.compute.internal   Ready      <none>   10m   v1.17.9-eks-4c6976   10.0.1.172    15.165.7.203   Amazon Linux 2   4.14.186-146.268.amzn2.x86_64   docker://19.3.6
~~~

참고로 워커 노드는 EC2 위에서 돌아가므로 워커 노드 개수를 늘렸다는 것은 구동 중인 EC2 개수가 늘어나는 것이므로 AWS 비용도 늘어나게 됩니다.  
그리고 추가 설정이 없을시 EKS를 위해 생성되는 EC2의 타입은 기본적으로 ```t3.medium```가 사용됩니다.  


<!--ads-->

# Helm 을 이용한 POD 배포 

## Helm Tiller 설정 

Helm 2.x를 사용 중이시라면 Helm Tiller를 Kubenertes에 ServiceAccount로 배포해야합니다.  
하지만 Helm 3.x를 사용 중이시라면 더 이상 Tiller 설치가 필요하지 않습니다. [참고 - helm init](https://helm.sh/docs/helm/helm_init/)  

이 글에서는 Helm 3.x를 사용한다고 가정하고 Tiller 설정에 대해서는 다루지 않겠습니다.  

## nginx-ingress 배포

Helm을 이용해서 ingress를 배포합니다.  

~~~terminal
$ helm install my-nginx stable/nginx-ingress --set rbac.create=true

WARNING: This chart is deprecated
NAME: my-nginx
LAST DEPLOYED: Fri Aug 28 17:38:11 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
~~~

해당 nginx-ingress는 deprecated 되었지만 Terraform, Helm을 이용한 EKS 구성을 테스트해보기에는 충분히 적당합니다.  

아래와 같이 ingress관련 2개의 pod가 running 중인 것을 확인할 수 있습니다.  

~~~terminal
$ kubectl get pod

NAME                                                     READY   STATUS    RESTARTS   AGE
my-nginx-nginx-ingress-controller-78b6584d64-p48nb       1/1     Running   0          3m4s
my-nginx-nginx-ingress-default-backend-ddfb86755-bsrkv   1/1     Running   0          3m4s
~~~




## Application 배포 

airflow 라는 어플리케이션을 배포해 볼 예정입니다.  

아래와 같이 airflow 관련 manifest.yaml 파일을 생성합니다.  
ingress가 ```/``` 경로 호출에 대해 airflow로 라우팅하도록 설정하고 있습니다.  

~~~terminal
$ cat > airflow-manifest.yaml <<EOF
ingress:
  enabled: true
  web:
    path: "/"
    annotations:
      kubernetes.io/ingress.class: "nginx"
EOF
~~~

그 후 helm으로 배포합니다.  

~~~terminal
$ helm install airflow stable/airflow -f airflow-manifest.yaml
~~~

아래와 같이 airflow 관련 6개의 pod가 추가 된 것을 확인할 수 있습니다.  

~~~terminal 
$ kubectl get pod

NAME                                                     READY   STATUS    RESTARTS   AGE
airflow-flower-7f54c64f55-ftspp                          1/1     Running   0          3m25s
airflow-postgresql-0                                     1/1     Running   0          3m25s
airflow-redis-master-0                                   1/1     Running   0          3m25s
airflow-scheduler-6c6f977b-q98n2                         1/1     Running   2          3m25s
airflow-web-7d977d5c47-ktwf6                             1/1     Running   2          3m25s
airflow-worker-0                                         1/1     Running   0          3m25s
my-nginx-nginx-ingress-controller-78b6584d64-p48nb       1/1     Running   0          27m
my-nginx-nginx-ingress-default-backend-ddfb86755-bsrkv   1/1     Running   0          27m
~~~

### 서비스 확인

이 글의 최하단에 있는 [AWS 계정](## AWS 계정) 설명에서 ELB 의 정보를 조회할 수 있는 권한설정을 다루고 있습니다.  
만약에 설정이 되어있다면 아래 명령어로 ELB의 DNS-NAME을 확인할 수 있습니다.  

~~~terminal
$ aws elb describe-load-balancers | grep DNSName

      "DNSName": "a9a7fff789cd84a7f881ex57fwe0f14e-1133921978.ap-northeast-2.elb.amazonaws.com",
~~~

만약 권한설정이 안되어있으시다면 AWS 콘솔에서 확인하는 방법도 있습니다. 브라우저에서 [AWS-ELB 관리화면](https://console.aws.amazon.com/ec2/v2#LoadBalancers:sort=loadBalancerName)으로 이동해서 ELB의 DNS 이름을 확인합니다.  

확인한 DNS-NAME을 가지고 브라우저에서 ```http://{elb-dns-name}``` 으로 접속해보면 airflow라는 웹화면에 접속되는 것을 확인할 수 있습니다.  

# Resources 제거 

## Pod 제거

아래 helm 명령어로 pod들을 제거 가능합니다.  

~~~terminal 
$ helm uninstall airflow
$ helm uninstall my-nginx
~~~

## EKS 제거

EKS와 같은 terraform으로 생성한 리소스들을 아래 명령어로 제거 가능합니다.  

~~~terminal 
$ terraform destroy
~~~

<!--ads-->


# 준비작업 

2020년도에 작성했던 준비작업을 가지고 2022년도에 다시 진행해보니 많은 내용이 바뀌었습니다.  
다시 진행하면서 변경된 사항을 최신화 해두지만 추후 시간이 지나면 또 변경사항들이 있을 것으로 예상됩니다.  
진행하시다가 잘 안되는 것이 있다면 링크 걸어둔 공식사이트를 참고하시기 바랍니다.  

## Install Terraform

[Hashicorp 공식사이트](https://learn.hashicorp.com/tutorials/terraform/install-cli)의 Ubuntu 설치 가이드를 보고 진행했습니다.  

~~~terminal
$ sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install terraform
~~~


## Install Helm

[Helm](https://helm.sh/docs/intro/install/#from-apt-debianubuntu)의 Ubuntu 설치 가이드를 보고 진행했습니다.  

~~~terminal
$ curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
$ sudo apt-get install apt-transport-https --yes
$ echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
$ sudo apt-get update
$ sudo apt-get install helm
~~~

Helm 2.x와 달리 Helm 3.x에서는 기본 repository가 설정되어있지 않습니다. 아래와 같이 repository를 추가해주셔야 합니다.  

~~~terminal
$ helm repo add stable https://kubernetes-charts.storage.googleapis.com/
$ helm repo update
~~~


## Install Kubectl

[Kubernetes](https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/)의 Ubuntu 설치 가이드를 보고 진행했습니다.  

~~~terminal 
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates curl
$ sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
$ echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
$ sudo apt-get update
$ sudo apt-get install -y kubectl
~~~

### Install AWS-IAM-AUTHENICATOR

Kubectl로 AWS EKS에 접근하기 위해서는 AWS-IAM-AUTHENICATOR가 필요합니다.  
[install-aws-iam-authenticator](https://docs.aws.amazon.com/ko_kr/eks/latest/userguide/install-aws-iam-authenticator.html)의 Ubuntu 설치 가이드를 보고 진행했습니다.  

~~~terminal
$ curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.7/2020-07-08/bin/linux/amd64/aws-iam-authenticator
$ chmod +x ./aws-iam-authenticator
$ mkdir -p $HOME/bin && mv ./aws-iam-authenticator $HOME/bin/aws-iam-authenticator && export PATH=$PATH:$HOME/bin
$ echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc
~~~

zsh 사용하신다면 마지막 명령어는 아래와 같이 바꿔서 실행하시면 됩니다.  

~~~terminal
$ echo 'export PATH=$PATH:$HOME/bin' >> ~/.zshrc
~~~

## AWS 계정

AWS 계정이 필요하고 terraform-eks 관련된 권한이 필요합니다.  

이미 사용 중이신 계정이 있다면 아래 ```cat``` 명령어에 게정의 aws_secret_access_key, aws_access_key_id 가 출력될 것입니다.  
출력되지 않는다면 계정 설정부터 진행해주시기 바랍니다.  

~~~terminal
$ cat ~/.aws/config

[default]
aws_secret_access_key = AAAAAAAAAAAAAAAAAAAAAAA/BBBBBBBBBBBBBBB
region = ap-northeast-2
aws_access_key_id = CCCCCCCCCCCCCCCCCCCCC
~~~

[AWS Provider - Authentication and Configuration](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication-and-configuration) 를 참고하시면 AWS access, secret key를 terraform에 전달하는 다른 방법도 확인하실 수 있습니다.  


[Terraform-AWS-EKS IAM 설정](https://github.com/terraform-aws-modules/terraform-aws-eks/blob/master/docs/iam-permissions.md)의 내용가진 IAM 정책을 하나 생성하고 사용 중이신 AWS 사용자 계정에 정책을 할당하시면 됩니다.  


그리고 ELB의 정보를 확인하기 위해서 아래 내용의 IAM 정책을 하나 더 만들어서 AWS 사용자 계정에 추가 할당하시면 됩니다.  

~~~json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "elasticloadbalancing:DescribeLoadBalancers"
            ],
            "Resource": "*"
        }
    ]
}
~~~




