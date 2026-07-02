# Kubernetes 클러스터 운영 및 관리

개인 실습으로 kubeadm 기반 멀티 노드 클러스터를 구축하고 주요 장애 시나리오를 검증했습니다. 이후 4인 팀 프로젝트에서는 클러스터 노드 상태 모니터링을 담당했습니다.

---

## Architecture

![Architecture](/architecture.svg)

## Tech Stack

`kubeadm` `kubectl` `ETCD` `Calico` `CoreDNS`

---

## 담당 역할

4인 팀 프로젝트에서는 kubectl 명령어로 클러스터 노드 상태를 주기적으로 확인하는 모니터링을 담당했습니다.

---

## 트러블슈팅

### 상황

개인 실습으로 클러스터를 v1.33.11에서 v1.34.7로 업그레이드하는 과정에서, kubelet 패키지 설치까지는 마쳤음에도 Worker 노드가 계속 NotReady 상태였습니다.

### 원인

로그를 추적한 끝에 daemon-reload와 kubelet 재시작이 빠졌다는 것을 확인했습니다. 패키지 설치가 끝났다고 작업이 끝난 게 아니었습니다.

### 해결

    systemctl daemon-reload
    systemctl restart kubelet

조치 후 10분이 채 되지 않는 시간 안에 Worker 노드가 다시 Ready 상태로 전환된 것을 확인했습니다.

### 배운 점

절차를 따라가는 것과 각 단계가 왜 필요한지 이해하는 건 다르다는 걸 실감했습니다. 서비스가 실제로 정상 작동하는 것까지 확인한 뒤에야 작업을 마무리했습니다.

---

## 검증 결과

**업그레이드 전**

    NAME       STATUS   ROLES           AGE   VERSION
    master     Ready    control-plane   12d   v1.33.11
    worker-1   Ready    <none>          12d   v1.33.11
    worker-2   Ready    <none>          12d   v1.33.11

**업그레이드 후**

    NAME       STATUS   ROLES           AGE   VERSION
    master     Ready    control-plane   12d   v1.34.7
    worker-1   Ready    <none>          12d   v1.34.7
    worker-2   Ready    <none>          12d   v1.34.7

조치 후 10분이 채 되지 않는 시간 안에 전체 노드가 Ready 상태(v1.34.7)로 정상 전환된 것을 확인했습니다.

---

## 발표 자료

[프로젝트 발표 자료 보기](https://github.com/gyu2001/kubernetes-cluster-management/blob/main/kubernetes-cluster-management.pdf)
