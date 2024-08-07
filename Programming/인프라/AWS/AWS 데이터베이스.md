
# RDS
 - 관계형 데이터 베이스 서비스
 - Aurora, PostgreSQL, MariaDB, Oracle, SQL Server 등의 RDS 엔진을 AWS에서는 제공
 - SQL 쿼리를 이용하는 데이터 베이스 용도에 사용
 - 3가지 데이터베이스 스토리지 유형 제공
	 - 범용 SSD 스토리지: 일반적인 용도
	 - 프로비저닝된 IOPS SSD 스토리지: 빠른 I/O가 필요한 경우 사용
	 - 마그네틱 스토리지: 엑세스 빈도가 낮은 경우 사용
## RDS 백업
- Amazon RDS는 DB 인스턴스 백업 및 복구를 위한 두 가지 방법, 즉 자동 백업 및 데이터베이스 스냅샷을 제공합니다.

### 자동백업
- 백업을 수행하는 백업기간을 설정
	- 백업 보존 기간은 1일 부터 최대 35일까지 설정 가능
	- DB 인스턴스를 특정 시점으로 복구 가능
	- RDS는 DB 트랜잭션 로그를 5분마다 백업 하므로 가장 오래된 시점 부터 5분전까지 시점으로 복구 가능합니다.
	- 자동백업을 비활성화 하려면 보존 기간을 0으로 설정

### 데이터베이스 스냅샷
- 사용자가 수동으로 스냅샷 생성가능
- 사용자가 지정한 만큼 백업을 보존할 수 있음(스냅샷 보존기간 없음)

- 특정 시점으로 복구 또는 DB 스냅샷에서 복구 작업을 수행하면 새로운 엔드포인트를 가지는 새 DB 인스턴스가 생성 됩니다.
- Amazon RDS DB 스냅샷과 자동 백업은 S3에 저장합니다.


## RDS - 읽기 전용 복제본(Read Replica)

- 읽기만 가능한 DB 인스턴스의 복제본을 여러 개 만드는 기능
- 읽기를 별도로 분리하여 성능을 향상
- 원본 DB 의 읽기/쓰기 트래픽을 분산시켜 성능 향상
- SQL 쿼리를 많이 하는 리포팅 툴의 경우 읽기 복제본으로 연결하여 쿼리 성능 향상
- 읽기 전용 복제본이 작동하려면 백업이 활성화된 상태로 유지되어야 합니다.
- 활성 상태의 장기 실행 트랜잭션이 있으면 완료 후에 읽기 전용 복제본을 생성하는 것을 권장하고 있습니다.


# Aurora

- RDS 호환형 관계형 데이터 베이스 입니다.
- RDS에서 제공하는 읽기전용 복제본, KMS 암호화, 스냅샷 백업, 오토스케일링 등을 제공
- AWS에서 만든 서비스로 다른 RDS보다 저렴한 비용에 성능이 더 뛰어납니다.
- 다른 RDS보다 속도는 3~5배 빠릅니다.
- 데이터베이스 설정, 패치 적용 및 백업과 같은 관리 태스크를 자동화합니다.
- 개별 DB 인스턴스 기반이 아닌 여러 인스턴스를 하나로 운영하는 클러스터 DB 기반으로 구성 됩니다.

## Aurora DB 클러스터
- 하나 이상의 DB 인스턴스와 이 DB 인스턴스의 데이터를 관리하는 클러스터 볼륨으로 구성됩니다.
- DB 인스턴스는 읽기/쓰기 작업을 하는 기본 DB 인스턴스와 읽기 작업만 하는 Aurora 복제본으로 구성됩니다.
- 각 Aurora DB 클러스터는 기본 DB 인스턴스에 더해 최대 15개까지 Aurora 복제본을 구성합니다.

## Aurora 복제본
- 3개의 가용영역에 6개의 데이터 사본을 자동 복제하여 고 가용성 및 성능 향상 지원
- 마스터 DB와 최대 15개의 Aurora Read Replica 지원
- 읽기 로드를 여러 복제 본에 분산시켜 성능을 향상시킬 수 있습니다.
- 마스터 DB 장애 발생시 최대 30초 이내에 복제본 중 하나가 기본 인스턴스 역할로 변경되는 장애 조치(Failover) 가능
- Aurora Auto Scaling을 사용해 워크로드에 따라 Aurora 복제본 수를 자동으로 조정이 가능합니다.

## Aurora 글로벌데이터베이스
- 다른 리전으로 데이터베이스 복제하는 기능을 지원
- 1초 미만의 대기 시간으로 최대 5개의 보조 리전에 복제 가능
- 보조 리전 중 하나가 1분 이내에 읽기 및 쓰기 기능으로 승격 가능
- 재해 복구 용도, 사용자가 가까운 리전에서 빠르게 엑세스 가능
  

## Aurora Database Cloning
- 현재 Aurora DB 클러스터를 복제하여 원본과 동일 데이터를 갖는 새 Aurora DB 클러스터를 생성하는 기능
- Snapshot을 만들고 복원하는 것보다 빠르고 비용 효율적입니다.
- Production DB 클러스터에 영향없이 테스트, 개발 등의 용도를 위한 Staging DB 클러스터 생성 가능


## AWS 멀티 마스터 클러스터

### 단일 마스터 클러스터
- 단일 DB 인스턴스는 모든 쓰기 작업을 수행, 기타 모든 DB 인스턴스는 읽기 전용입니다. Writer DB 인스턴스가 사용 불가 상태가 되면 장애 조치 메커니즘이 읽기 전용 인스턴스 중 하나를 새 라이터로 승격

### 멀티 마스터 클러스터
- 모든 DB 인스턴스는 쓰기 작업을 수행
- Writer DB 인스턴스가 사용 불가 상태가 될 때 어떤 장애 조치도 없습니다.
- 읽기/쓰기 DB 인스턴스가 사용 불가 상태가 될 때 장애 조치 프로세스 및 관련 지연이 발생하지 않습니다.


## Aurora Serverless
- DB 인스턴스 운영 및 데이터베이스 용량을 수동으로 관리하지 않음
- 특정 DB 인스턴스 유형을 선택하지 않습니다.
- 사용량에 따라 DB 용량을 자동으로 빠르게 용량을 확장하고 축소하는 기능지원
- 사용한 만큼만 DB 용량을 초당 요금으로 지불
- DB 사용빈도가 낮은 애플리케이션에 효과적입니다.