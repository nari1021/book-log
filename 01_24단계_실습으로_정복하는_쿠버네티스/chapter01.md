# 01. 쿠버네티스 개요와 클러스터 설치

## 1. 쿠버네티스란?

- 대모 클러스터 환경에서 컨ㅔ이너화된 애플리케이션을 자동으로 배포하고 확장, 관리하는 데 필요한 여러 가 요들을 자동화하는 오픈소스 플랫폼
- 클러스터의 전체 상태를 지속적으로 확인하면서 문제가 생긴 애플리케이션은 자동으로 재시작 함
- 쿠버네티스 자동 복구 기능 : 실행(Running) > 종료(Terminating) > 생성(ContainerCreating) > 실생(Running)

## 2. Kubespray를 이요해 3개의 노드로 구성된 클러스터 구축

-> Azure AKS를 이용해서 테스트할 예정이라 설치하지 않음

## 3. K3s를 이용해 단일 노드로 구성된 클러스터 구축

-> Azure AKS를 이용해서 테스트할 예정이라 따로 하지 않음

## 4. 로컬호스트에서 원격 쿠버네티스 관리하기

1. [kubectl 설치](https://kubernetes.io/ko/docs/tasks/tools/)

kubectl은 쿠버네티스 명령어로 파드를 생성/조회/삭제하는 등 쿠버네티스 관리 업무를 수행할 수 있다.

```shell
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
$ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

# 설치한 버전 확인
$ kubectl version --client
$ kubectl version --client --output=yaml
```

- `-L (--location Follow redirects)` : 서버 링크가 다른 URL로 re-direct되어 있는 경우, redirect URL까지 접속
- `-O (--remote-name Write output to a file named as the remote file)` : 서버에 올라간 파일을 그대로 다운로드 하는 것

2. 원격 클러스터 정보를 kubeconfig 파일에 등록

- 쿠버네티스는 원격 클러스터의 API 서버 통신에 필요한 인증 정보를 kubeconfig(${HOME}/.kube/config) 파일에서 관리
- kubeconfig에는 원격 클러스터의 관리에 필요한 인증 정보가 저장됨

```shell
# 원격 클러스터에서 확인
$ cat /root/.kube.config
```

위 명령어로 나온 내용을 로컬에 만들어서 똑같이 생성해주면, 로컬호스트에서 원격 클러스터 관리 가능
