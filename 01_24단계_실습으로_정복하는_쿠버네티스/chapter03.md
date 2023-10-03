# 03. kubectl 명령어로 익히는 쿠버네티스의 주요 오브젝트

- run, create : 파드와 디플로이먼크 생성
- get, exec : 생성된 파드 현황 조회 및 파드 내 bash 스크립트 실행 (파드 접속)
- scale, delete : 파드의 수량 증가/감소 및 오브젝트 삭제
- create namespace : 네임스페이스 생성

쿠버네티스 오브젝트란, 쿠버네티스 API 서버로 생성하는 영속성을 가지는 모든 실체를 말한다.
위에서 언급한 파드, 디플로이먼트, 네임스페이스는 모두 쿠버네티스에서 사용하는 오브젝트이다.

쿠버네티스의 모든 오브젝트는 API 서버로 생성한다.
사용자가 CLI를 이용하여 쿠버네티스 API를 호출해서 오브젝트를 실행하는 방식으로 동작한다.

## 1. NGINX 파드 실행과 배시 실행

파드는 쿠버네티스 환경에서 컨테이너 애플리케이션을 실행하는 기본 단위이다. 일반적으로 단일 컨테이너만 실행하지만 2개 이상의 컨테이너도 하나의 파드 안에서 실행 가능하다.

일반적으로 파드라는 용어는 컴퓨팅, 네트워크, 스토리지를 모듈 형태로 묶어서 시스템 확장 시 사용하는 기본 단위를 의미한다.쿠버네티스에서도 파드는 다른 파드와 구분되는 고유한 네트워크와 스토리지를 가진다. 애플리케이션 확장 시 파드 단위로 증설한다.

```shell
## nginx 이미지를 가진 파드를 실행
$ k run nginx --image=nginx
pod/nginx created

## 실행 중인 파드 목록 확인
$ k get pod
```

쿠버네티스 상의 모든 오브젝트 목록을 확인할 때는 `k get {object name}` 명령어를 사용한다.
IP 주소를 포함한 더 상세한 정보는 -o wide 를 추가해서 `kubectl get pod -o wide` 로 확인한다.

모든 네임스페이스의 파드를 조회

```shell
## -A(--all-namespaces)
$ k get pod -A -o wide
```

파드는 고유한 IP주소를 가지며, 각 파드는 각각 고유한 볼륨(데이터)를 사용한다.
기본적으로 개별 파드가 사용하는 볼륨은 호스트 노드의 `/var/lib/containers/{pod name}` 디렉터리이다. 해당 디렉터리에 각 파드별로 디렉터리가 구분돼 있다. 이처럼 파드별로 각기 고유한 네트워크와 스토리지를 가진다.

쿠버네티스에서는 파드 접속이 파드에 배시(bash)를 실행(exec)하는 것과 동일하다.

```shell
## 파드 접속(파드 bash 실행)
$ k exec -it nginx -- bash
root@nginx:/# ps
bash: ps: command not found

## ps를 실행하기 위해 procps 패키지 설치
root@nginx:/# apt -u update && apt -y install procps

## 파드내의 전체 프로세스 목록 확인
root@nginx:/# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0  11380  7320 ?        Ss   14:05   0:00 nginx: master process nginx -g daemon off;
nginx         29  0.0  0.0  11844  2792 ?        S    14:05   0:00 nginx: worker process
nginx         30  0.0  0.0  11844  2792 ?        S    14:05   0:00 nginx: worker process
nginx         31  0.0  0.0  11844  2792 ?        S    14:05   0:00 nginx: worker process
nginx         32  0.0  0.0  11844  2792 ?        S    14:05   0:00 nginx: worker process
root          39  0.0  0.0   4188  3536 pts/0    Ss   14:12   0:00 bash
root         229  0.0  0.0   8100  4080 pts/0    R+   14:13   0:00 ps aux
```

컨테이너는 가상 머신과는 다르게 특정 프로세스만 실행 중이다.
그래서 이미지 크기가 작고 실행 속도 역시 몇 초 내외로 매우 빠르다.

## 2. 디플로이먼트의 파드 개수 변경과 삭제

파드 개수를 변경하려면 쿠버네티스 오브젝트 타입을 파드가 아닌 디플로이먼트(deployment)로 실행해야 한다.
디플로이먼트는 파드가 배포(deploy)되는 방법을 정의하는 오브젝트로서 파드의 개수, 이미지 종류, 배포 방법 등을 정의한다.

디플로이먼트는 파드처럼 kubectl run 이 아닌 `kubectl create deployment` 명령어를 사용한다.

디플로이먼트는 생성하면, httpd-65bfffd87f-kw6sm `<디플로이먼트이름> + <임의 해쉬값>` 으로 파드의 이름을 지정한다.

kubectl scale 명령어는 인자로 디플로이먼트 이름과 수량을 받는다.

```shell
## --replicas 에 원하는 파드의 수량을 입력한다.
$ k scale deployment httpd --replicas 3
deployment.apps/httpd scaled

## 실시간으로 변경되는 파드의 수량 확인
$ k get pod -w

## --replicas=0 이면 파드 수량이 0개가 된다.
$ k scale deployment httpd --replicas=0

## 파드 삭제
$ k delete pod httpd-65bfffd87f-v4nvj
pod "httpd-65bfffd87f-v4nvj" deleted

## 기존 파드는 삭제되고 자동으로 새로운 파드가 생성된다.
$ k get pod -w
NAME                     READY   STATUS    RESTARTS   AGE
httpd-65bfffd87f-c4rl7   1/1     Running   0          10s
nginx                    1/1     Running   0          49m
nginx01                  1/1     Running   0          46m
```

쿠버네티스는 항상 의도한 상태를 자동으로 유지하려고 하기 때문에 디플로이먼트는 속성으로 파드의 수량을 가진다.
이처럼 자동으로 파드를 의도한 상태로 복구하는 것을 `자동 복구` 라고 한다.

## 3. 네임스페이스 생성

쿠버네티스 오브젝트는 create 명령어로 생성할 수 있다.

```shell
## ns=namespaces, 네임스페이스를 줄여서 ns로 사용할 수 있음
$ k create ns default01
namespace/default01 created
```

네임스페이스는 클러스터를 구분하는 가상 클러스터 단위이다.
같은 네임스페이스 내에서는 같은 이름의 오브젝트를 만들지 못하지만 네임스페이스가 다르면 같은 이름의 오브젝트를 생성할 수 있다.

현업에서는 네임스페이스를 주로 애플리케이션을 구분하는 단위로 사용한다.
예를들어 웹(NGINX), 웹 애플리케이션 서버(Tomcat), 데이터베이스(MySQL), 레디스(Redis) 등 각 애플리케이션마다 네임스페이스를 별도로 지정한다.

하지만 네임스페이스는 클러스터를 가상으로 구분하는 단위라서 물리적으로 완전하게 분리하지는 못한다.
기본적으로 임의의 네임스페이스에서 다른 네임스페이스로 네트워크 연결이 가능하다.

ping을 실행하기 위해서 `iputils-ping` 패키지를 설치해서 확인할 수 있다.

```shell
$ apt-get update -y
$ apt-get install iputils-ping -y
$ ping 10.000.000.000 -c 1
```

즉, 네임스페이스는 네트워크까지 차단되어 물리적으로 완전히 분리되는 환경을 제공하지는 않고 가상 수준에서 클러스터를 분리할 수 있다.
