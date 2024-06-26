# Target Group 개요
ELB와 연결할 수 있는 Target Type으로는 하기와 같은 구성요소들이 존재합니다.

- EC2 인스턴스(개별 EC2 인스턴스, EC2 Auto Scaling Groups)
- IP 주소 
- Lamada 함수(오직 Application Load Balancer만 연결이 가능합니다.) 
- Application Load Balancer(Network Load Balancer만 연결이 가능합니다.)


## 속성(Attributes) - HTTP / HTTPS
Application Load Balancer에서 사용합니다.

등록 취소 지연(Deregistration delay / Connecting Draining): Auto Scaling 축소 등으로 등록취소 된 인스턴스에 더 이상의 요청을 보내지 않도록 하는 기능, 해당 인스턴스에 진행중인 요청이 있을 경우 설정해 놓은 시간동안 연결이 유효상태가 되지 않으면 해당 인스턴스에 연결을 요청하지 않습니다.

느린 시작 기간(Slow start duration): 기본적으로 대상 그룹으로 등록되자 마자 전체 요청 공유를 받기 시작하고 초기 상태 확인을 전달, 느린시작 모드에서는 로드 벨런서가 대상으로 보낼 수 있는 요청의 수를 선형으로 증가시킵니다.

알고리즘
- 라운드 로빈(Round-Robin): 일정 시간마다 라우팅을 변경
- 최소 미해결 요청(Least Outstadning Requests): 처리하고 있는 요청이 가장 적은 대상에게 라우팅

고정(Stickiness Sessions / Session Affinity)
- 클라이언트가 세션을 유지한 상태라면 모든 요청을 동일한 인스턴스로 유지하는 기능
## 교차 영역 로드 밸런싱(Cross-Zone Load Balancing)

- 교차영역 로드 밸런싱 비활성화: 가용영역 내에 있는 타겟에만 트래픽을 분배
- 교차영역 로드 밸런싱 활성화: 모든 가용영역의 등록된 모든 타겟 인스턴스에 동일하게 트래픽을 분배


## NLB(Network Load Balancer)

TCP, UDP, TLS 요청을 로드밸런싱 해야 하는 경우에 사용합니다.  초당 수백만 개의 요청과 갑작스럽고 변동성이 높은 트래픽 패턴을 처리하도록 설계되어 있으며 대기시간이 매우 짧습니다.

주로 게임 등의 수백만의 동시 사용자 처리에 적합하고, 고도의 성능이 요구되거나 대기시간이 낮아야하는 애플리케이션에 적합합니다.

고정 IP 주소 할당이 가능하고, 클라이언트 IP 주소 전달이 가능합니다.
그리고 데이터 전송보안을 위한 TLS 프로토콜 사용 시 SSL/TLS 인증서를 배포해야 합니다.

> 인증서는 ACM(AWS Certificate Manager) 사용 또는 클라이언트 인증서 사용 가능합니다.