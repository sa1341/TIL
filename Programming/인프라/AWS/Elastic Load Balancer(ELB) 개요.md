# Target Group 개요
ELB와 연결할 수 있는 Target Type으로는 하기와 같은 구성요소들이 존재합니다.

- EC2 인스턴스(개별 EC2 인스턴스, EC2 Auto Scaling Groups)
- IP 주소 
- Lamada 함수(오직 Application Load Balancer만 연결이 가능합니다.) 
- Application Load Balancer(Network Load Balancer만 연결이 가능합니다.)


## 속성(Attributes) - HTTP / HTTPS
Application Load Balancer에서 사용합니다.

등록 취소 지연: Auto Scaling 축소 등으로 등록취소 된 인스턴스에 더 이상의 요청을 보내지 않도록 하는 기능, 해당 인스턴스에 진행중인 요청이 있을 경우 설정해 놓은 시간동안 연결이 유효상태가 되지 않으면 해당 인스턴스에 여