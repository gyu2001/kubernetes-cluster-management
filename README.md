# Kubernetes 클러스터 운영 및 관리

컨테이너 오케스트레이션 환경의 이해를 위한 멀티 노드 클러스터 구축 및 주요 컴포넌트별 장애 시나리오 검증

---

## 🏗 Architecture

[ Control Plane ]
                  (kubeadm)
                /      |      \
          [ETCD]  [API Server]  [Scheduler]
                \      |      /
                [ Worker Node 1 ]
                [ Worker Node 2 ]
                     |
                [ Calico CNI ]
                [ CoreDNS ]

---

## 🛠 Tech Stack

`kubeadm` `kubectl` `ETCD` `Calico` `CoreDNS`

---

## 📌 주요 구현 내용

### 1. 멀티 노드 클러스터 구축
- kubeadm 기반 control plane 및 worker 노드 구성
- Calico CNI 설치로 파드 간 네트워크 통신 구성
- CoreDNS 기반 클러스터 내부 DNS 설정

### 2. 보안 및 접근 제어
- ServiceAccount 및 RBAC 설정을 통한 최소 권한 제어 규칙 수립
- Network Policy 적용으로 파드 간 불필요한 통신 차단

### 3. 데이터 영속성 확보
- PV/PVC 구성을 통한 컨테이너 데이터 영속성 확보
- ETCD 스냅샷 백업 스크립트 작성 및 복구 절차 검증

---

## 👤 담당 역할

| 구분 | 내용 |
|---|---|
| 팀 프로젝트 | 노드 상태 모니터링 및 관리 담당. kubectl 기반 노드 상태 확인 및 RBAC 설정 수행 |
| 독립 재구현 | 클러스터를 v1.33.11에서 v1.34.7로 업그레이드하는 전 과정을 혼자 재구현 |

---

## 🔧 트러블슈팅

### Worker 노드 NotReady 장애 해결

**상황**
클러스터 업그레이드 중 worker 노드가 NotReady 상태로 남아 한참을 헤맴

**원인**
kubelet 패키지 설치 후 daemon-reload와 restart를 빠뜨림

**해결**
```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

**배운 점**
절차를 따라가는 것과 각 단계가 왜 필요한지 이해하는 건 다르다는 걸 실감함. 이후 모든 작업에서 각 단계를 제거하거나 변경해보며 이유를 직접 확인하는 습관이 생김
