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
