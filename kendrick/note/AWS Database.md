# AWS Database
## Amazon RDS

> Amazon Relational Database Service(Amazon RDS)는 AWS 클라우드에서 관계형 데이터베이스를 더 쉽게 설치, 운영 및 확장할 수 있는 웹 서비스
>
> https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/Welcome.html

- 지원 엔진
  - MySQL (+ Aurora)
  - MariaDB
  - PostgreSQL (+ Aurora)
  - Oracle
  - Microsoft SQL Server

### EC2 & DB 직접 프로비젼 VS RDS
- RDS를 써야 할 때
  - 비즈니스 본연의 가치에 집중하려는 경우
  - 데이터베이스 관리 업무를 원치않는 경우
  - 하이 레벨 튜닝 및 스키마 최적화 등의 업무만 처리하려는 경우
  - 회사내 데이터베이스 관리 역량이 충분치 않은 경우
  - 푸시 기반 디중 AZ 복제 환경을 이용하는 경우
  - 백업 및 복원 자동화를 원하는 경우
- EC2를 써야할 때
  - 데이터베이스 인스턴스에 대한 완전한 통제권을 원하는 경우
  - 운영 체제 시스템에 대한 통제권을 원하는 경우
  - 백업, 복제, 클러스터링 등에 대한 완전한 통제권을 원하는 경우
  - 기업의 RDBMS 엔진의 주요 기능과 옵션이 Amazon RDS와 맞지않는 경우
  - 기업에서 요구하는 용량 및 성능이 Amazon RDS의 제공수준을 넘어서는 경우
