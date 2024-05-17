
## Serverless란?

AWS Lamda는 Serverless라는 주요 장점이 있습니다. Serverless는 서버를 사용자가 관리할 필요가 없다는 의미지 실제로 서버는 존재하고, 서버 인프라 운영은 AWS 등의 클라우드 회사에서 담당합니다.

- AWS에서 용량 조정, 프로비저닝, 패치 등의 인프라를 관리합니다.
- 사용자는 필요한 애플리케이션을 구축해서 사용하기만 하면 됩니다.


대표적인 AWS 서버리스 서비스로는 하기와 같이 있습니다.

- AWS Lamda, AWS Fargate
- Amazon S3, DynamoDB, Amazon Aurora Serverless
- Amazon SNS, Amazon SQS, API Gateway


## Lamda 개요

- 코드를 실행하여 동작하는 서버리스 컴퓨팅
- 다양한 프로그래밍 언어를 지원(Java, Node.js, Python, C#, Ruby 등)
- AWS Lamda 함수는 실행당 최대 15분 동안 하도록 구성
- Lamda는 AWS에서 서버 운영에 필요한 모든 인프라를 관리
- AWS Lamda 함수는 실행당 최대 15분 동안 하도록 구성(1초에서 15분 사이의 값으로 제한 시간을 설정)
- EC2는 Auto Scaling 기능을 사용해 서버를 확장하지만 Lamda는 사용량이 늘어나면 자동으로 용량이 확장되므로 용량 계획이 필요 없고 확장성이 뛰어납니다.