# 02. 효율적인 쿠버네티스 클러스터 관리를 위한 kubectl CLI 환경 최적화

## 1. kubectl 자동 완성과 명령어 앨리어스 활용

- [kubectl autocomplete](https://kubernetes.io/ko/docs/tasks/tools/included/optional-kubectl-configs-bash-linux/) 이용하기

## 2. 쿠버네티스 krew를 이용한 플러그인 관리

- krew란, kubectl 플러그인 매니저로서, kubectl 커맨드라인 환경에서 사용 가능한 다양한 플러그인을 설치, 삭제, 조회하는 기능을 제공한다.
- [krew 설치하기](https://krew.sigs.k8s.io/docs/user-guide/setup/install/)

## 3. kube-ctx(컨텍스트), kube-ns(네임스페이스), kube-ps1(포롬프트) 활용

- kube-ctx(context, 문맥)는 쿠버네티스 컨텍스트를 선택
- kube-ns(namespace, 네임스페이스)는 단일클러스터 내에서 각 네임스페이스별 자원을 격리할 수 있어서 가상 클러스터로 구분하는 용도로 사용
- kube-ps1(prompt, 사용자에게 보여지는 메시지)는 현재 클러스터와 네임스페이스의 이름을 리눅스 프롬프트에 표시
