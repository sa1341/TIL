
AWS는 전 세계 21개의 지리적 리전 내에 66개의 가용영역을 운용하고 있어, 장애 대처에 안정적이며, 확장 가능한 방식으로 설계되었습니다.

- 각 리전은 개별 지역 내 존재하는 지리적 위치를 의미합니다.
- 가용영역(AZ)은 리전 내 있는 구분된 가용성 영역을 의미합니다. 전용선으로 연결되어 있어 마치 한 클러스터인 것처럼 동작합니다.
- 이렇게 지역별로 지역내에서도 가용영역을 분리하여 강력한 내결함성과 안정성을 얻을 수 있는 이점이 있습니다.
## 리전(Region)
- 데이터 센터를 클러스터링 하는 물리적 위치(서울리전, 홍콩리전)
- 전세계 주요국가에 위치
- 1개 AWS 리전 = 2개 이상의 가용영역으로 구성
- 대부분의 AWS 서비스는 리전을 선택하여 시작(예, EC2 서비스)
- 리전을 선택하지 않는 글로벌 서비스도 있음(예, IAM 서비스)
- 재해복구(DR) 설계 = 2개 이상의 리전에 시스템을 배치
## 가용영역(Availability zone - AZ)
- 가용영역 = 하나 이상의 개별 데이터 센터
- 1개의 리전은 2개이상의 가용영역으로 구성 (보통 3~4개의 가용영역으로 구성)
- 가용영역끼리는 물리적으로 떨어져 있고 고속 네트워크로 연결됨
- 고가용성 설계 = 다중 AZ(Multi-AZ), 2개 이상의 가용영역에 시스템 배치
## 엣지 로케이션(Edge Location)
- 엣지 로케이션에 콘텐츠(데이터)를 캐싱하여 사용자에게 더 짧은 지연 시간으로 콘텐츠를 전송
- 글로벌  배포서비스인 AWS CloudFront, Global Accelerator에서 대표적으로 사용

> 사용자가 콘텐츠를 최초 요청하면 서버가 있는 리전의 오리진에서 사용자와 가까운 Edge Location으로 전송합니다. 사용자가 이후 동일한 콘텐츠를 요청하면 Edge Location에서 콘텐츠를 전송합니다.

## EC2 스토리지 - Amazon EBS
EBS는 EC2에서 사용하는 일종의 하드디스크입니다. 다른 물리적 하드 드라이브처럼 사용 가능하고, 다른 인스턴스에 EBS를 분리한 후 다른 인스턴스에 연결하는 것도 가능하다.

## Amazon S3
Simple Storage Service의 약자로 파일 서버의 역할을 하는 서비스입니다.

일반적인 파일서버는 트래픽이 증가함에 따라서 장비를 증설하는 작업을 해야 하는데 S3는 이와 같은 것을 대행합니다.

저장할 수 있는 파일 개수의 제한이 없으며, 데이터 손실이 발생할 경우 자동으로 복구하며, 정보에 중요도에 따라 보호 수준을 설정해 비용을 절감할 수 있습니다.