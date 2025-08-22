# Prometheus, Grafana 실습 과정 및 개념 정리

본 실습은 NKS(Ncloud Kubernetes Service)를 이용하여 진행되었습니다. 
로컬에서 돌리실 경우 로컬 클러스터를 포트 포워딩하여 배포하셔도 무방합니다. 

---
# kube-prometheus-stack 구성 요소 정리
> Helm 차트 **kube-prometheus-stack** 설치 시 생성되는 주요 리소스들  

---

# Prometheus - 메트릭 수집 & 저장  
(리소스: `prometheus-kps-kube-prometheus-stack-prometheus-0`)
- Pod/Node의 CPU, 메모리, 네트워크 등 수치 데이터 수집
- 알람 조건 설정

동작 방식:
- 직접 대상 (노드, 파드, 서비스)에 접근하여 Pull 방식으로 메트릭을 가져옴  

---

# Grafana - 시각화 & 대시보드  
(리소스: `kps-grafana-xxxxx`)
- Prometheus 데이터를 시각화 하여 대시보드로 표현
- 커스텀 마이징 대시보드 생성 가능
- Alertmanager와 연동해 이메일, 슬랙 알림 발송  

---

# Alertmanager - 알람 처리 & 전송  
(리소스: `alertmanager-kps-kube-prometheus-stack-alertmanager-0`)
- Prometheus가 보낸 알람을 수신
- 알림을 그룹핑하고, 억제(Silence) 및 재시도 관리
- Slack, Teams, 이메일 등 외부 채널로 전송  

---

# Prometheus Operator - 자동 관리  
(리소스: `kps-kube-prometheus-stack-operator-xxxxx`)
- Prometheus, Alertmanager, Grafana를 쿠버네티스 CRD 기반으로 관리
- ServiceMonitor, PodMonitor 리소스를 감시해 자동 타겟 등록
- Prometheus 설정 변경을 자동 반영  

---

# kube-state-metrics - 쿠버네티스 상태 메트릭 제공  
(리소스: `kps-kube-state-metrics-xxxxx`)
- Deployment, DaemonSet, Pod, Node 등의 상태를 메트릭으로 변환
- 클러스터 리소스 상태 확인 가능
- 예: `kube_pod_status_ready`, `kube_deployment_replicas`  

---

# Node Exporter - 노드 리소스 메트릭 제공  
(리소스: `kps-prometheus-node-exporter-xxxxx`)
- CPU, 메모리, 디스크, 네트워크 등 OS 레벨 리소스 메트릭 수집
- 각 워커 노드에 DaemonSet으로 배포되어 동작
- 예: `node_cpu_seconds_total`, `node_memory_MemAvailable_bytes`

--- 
# 프로메테우스, 그라파나 설치 과정
1) Helm 설치 
$ curl -fsSL https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash 
$ helm version

2) Helm repo 추가 & kube-prometheus-stack 배포 
$ helm repo add prometheus-community https://prometheus-community.github.io/helm-charts 
$ helm repo update 
$ kubectl create namespace monitoring 
$ helm install kps prometheus-community/kube-prometheus-stack -n monitoring 

3) Grafana 접속
# LoadBalancer로 타입 변경 
$ kubectl -n monitoring patch svc kps-grafana -p '{"spec":{"type":"LoadBalancer"}}' 
# EXTERNAL-IP 할당 대기(별도 명령) 
$ kubectl -n monitoring get svc kps-grafana -w

4) 테스트용 Pod 배포 
$ kubectl run nginx --image=nginx --port=80 
$ kubectl expose pod nginx --name=nginx-svc --port=80 --target-port=80
