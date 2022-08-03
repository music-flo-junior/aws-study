# Amazon EC2

> Amazon Web Services(AWS) 클라우드에서 확장 가능 컴퓨팅 용량을 제공하는 서비스
>
> EC2 Instance: 가상 컴퓨팅 환경 (서버)

reference: https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/concepts.html

## EC2 장점
- 빠른 제품 출시
- 확장성
- 통제성
- 신뢰성
- 보안성
- 다양한 인스턴스 타입
- (다른 AWS Service와의) 통합
- 비용 효율성

## Instance Type

### 범용 인스턴스 (General Purpose)
- T3, T3a, T2, M6g, M5, M6a, M5n, M4, A1
- burstable performance
   - 기존 CPU 활성화 수준을 능가해서 사용가능
   - CPU 사용량에 따라 credit balance에 CPU Credit 축적 -> 성능 가속이 필요할 때 CPU Credit 사용해서 CPU 성능 가속

### 컴퓨트 최적화 인스턴스 (Compute Optimized)
- C6g, C5, C5a, C5n, C4
- 고도의 컴퓨팅 파워가 요구되는 워크로드 처리용
- 미디어 변환, 동접자 높은 애플리케이션, 장기간의 배치 프로세싱, 게임 서버 등

### 메모리 최적화 인스턴스 (Memory Optimized)
- R6g, R5, R5a, R5n, R4, X1e, X1, High Memory, 21d
- 고성능 메모리가 요구되는 워크로드 처리용
- SAP HANA, Oracle In-Memory DB, MongoDB 혹은 Cassandra 등의 NoSQL DB, Presto, Apache Spark와 같은 빅데이터 엔진, High-performance Computing (HPC) 애플리케이션, Electronic Design Automation (EDA) 애플리에키션, 유전자 데이터 수집 및 분석 등

### 스토리지 최적화 인스턴스 (Storage Optimized)
- 13, 13en, D2, H1
- 로컬 스토리지에서 대규모 데이터 세트를 순차적으로 읽기 및 쓰기 방시그올 접근하는 것과 같은 워크로드 처리용
- Relational DB, NoSQL DB, DW 애플리케이션, Redis 등 In-Memory DB의 MapReduce 및 Hadoop 분산 캐시 등

### 고성능 컴퓨트 인스턴스
- P3, P2, lnf1, G4, G3, F1
- Grpahic Processing Units (GPU) 및 FPGAField Programmable Gate Arrays 등의 하드웨어 기반 가속기 지원
- 머신러닝, 세포 모델링, 유전공학, 유체역학, 자율주행, 신약개발, 지질탐사, 금융공학 등

## 프로세서 특징
- Intel Processor 사용
  - Intel AES (Advanced Encryption Standard) New Instructions
  - Intel Advanced Vector Extensions (AVX)
  - Intel Turbo Boost Technology
  - Intel Deep Learning Boost

## 네트워크 특징
- 항상 VPC 내부에 론칭할 것 (네트워크 매니징에서의 이점)
- Placement Group: **단일 AZ 내**에서 인스턴스를 논리적으로 묶는 방법 (Cluster Networking)
  - Placement Group에서 인스턴스 론칭 시, 높은 수준의 네트워크 성능
  - 인스턴스 타입은 가급적 통일
  - 이미 존재하는 인스턴스 PG에 추가 불가
  - 향상된 네트워킹을 지원하는 인스턴스 타입으로 구성하면 시너지 효과

## 스토리지 특징
- EBS Volume: 프라이머리 스토리지 기능 (하드 드라이브)
  - 똇다 붙였다 가능 - Instance 생명 주기와 별개
- 범용 스토리지 (GP2)
  - SSDs (Solid-State Drives) 기반
- Provision IOPS (PIOPS) 스토리지:
  - 대량의 I/O 작업이 필요한 워크로드 용
  - 범용 보다 좀 더 비쌈
- Magnetic 스토리지
  - 기가바이트당 비용이 최저
  - 비교적 중요도가 낮은 워크로드 용

- Instance Store
  - Physical Hardware Local Disk
  - Ephemeral Storage
  - Instance와 같은 생명주기

- EBS 최적화 인스턴스들은 EBS에서 최대치의 PIOPS 활용가능

## 가격 정책

### 온디맨드 인스턴스
- 시간당 or 초당 과금
- 가장 대표적인 모델

### 예약 인스턴스
- 온디맨드에 비해 최대 75%까지 할인 가능
- 워크로드에 얼마만큼의 리소스가 얼마나 오래 사용되는지 알고 있을때 사용
  - e.g. 16 CPU의 컴퓨팅 파워를 최소 1년간 사용할 계획
- 지불 옵션
  - up-front reserved (선불)
  - partial up-front reserved (일부 선불)
  - no up-front reserved (매월 지불)

- 리전 예약 인스턴스: 특정 리전 인스턴스 예약
- 존 예약 인스턴스: 특정 AZ 인스턴스 예약
- 표준 예약 인스턴스: 일반 적인 예약 인스턴스
- 전환 예약 인스턴스: 도중에 인스턴스 타입 변경 가능

### 스팟 인스턴스
- AWS 여유 컴퓨팅 자원에 대한 경매 입찰 방식
- 온디맨드 대비 최대 90% 할인 가능
- 내가 쓰다가 다른 사람이 더 높은 금액 (spot price)을 제시하면 나는 못씀
- EC2가 2분 단위로 스팟 인스턴스 계속 요청
- 중요도가 낮은 워크로드 (도중에 끊겨도 되는)에 적합
  - 컨테이너화된 모든 것
  - Apache Spark 및 Hadoop 같은 빅데이터 프레임워크
  - 빅데이터 일괄 처리
  - 스테이트리스 (Stateless) 웹서비스
  - 머신러닝(PyTorch, Tensorflow, 대규모 훈련 잡)
  - Jenkins를 이용한 CI/CD
  - 염기서열 분석 등 고성능 컴퓨팅(HPC)
  - 인스턴스를 유연하게 사용할 수 있는 내오류성 작업 (fault tolerant) 또는 스테이트리스 작업
- spot fleet
  - 다수의 스팟 인스턴스 혹은 온디맨드 인스턴스로 구성
  - 서로 다른 타입의 인스턴스 혼합하는 것도 좋음
  - Failover 가능
- Best Practice: 기본 설정의 최대 가격 (온디맨드 가격) 옵션을 적용
  - 항상 최고가 입찰 가능

## 공유 테넌시, 전용 호스트, 전용 인스턴스
- compliance requirements (규정 요건 충족) 때문에 가상화된 인스턴스의 성능이 기대 요구치보다 낮아질 수 있음 -> 전용 호스트, 전용 인스턴스로 해결 가능

### Shared Tenancy (공유 테넌시)
- 기본 적용 (default) 가상화 조건
- multitenant hardware에서 instance 실행

### Dedicated Host (전용 호스트)
- 특정 사용자에게만 배타적으로 할당된 물리적 서버
- 기업의 필요에 따라 다양한 소프트웨어 탑재해서 사용 가능 -> 비용 효율성
- VM 내가 원하는 만큼 생성 가능

### Dedicated Instance (전용 인스턴스)
- 개별 사용자에게만 할당된 하드웨어의 VPC에서 인스턴스 실행
- `단일` 테넌트 하드웨어에서 EC2 인스턴스 실행
- 다른 AWS 사용자와 물리적으로 격리된 환경
- bare-metal instance: 하이퍼 바이저 x. 서버의 프로세서 및 메모리 자원에 직접 접근 가능
  - 비가상화 환경에서 실행해야하는 애플리케이션에 적합

### Dedicated Host VS Dedicated Instance

- [이해에 도움이 되었던 자료](https://stackoverflow.com/questions/64309679/aws-dedicated-host-vs-dedicated-instance-why-the-first-is-more-expensive-than)

| | Dedicated Host | Dedicated instance |
|:---:|:---:|:---:|
|결제 | 호스트 단위 결제 | 인스턴스 단위 결제 |
| 소켓,코어 및 호스트 ID 표시 여부 | 소켓 및 물리 코어 수 표시 여부 제공 | 표시 x |
| 호스트 및 인스턴스 선호도 | 시간에 따라 지속적으로 동일한 물리 서버에 인스턴스 배포 허용 | 지원 x |
| 대상 지정 인스턴스 배치 | 물리 서버 내 인스턴스 배치 방법에 대한 추가 가시성 및 제어 제공 | 지원 x |
| 자동 인스턴스 복구 | [호스트 복구](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/dedicated-hosts-recovery.html) | 지원 o |
| Bring Your Own License(BYOL) | 지원 o | 지원 x |

https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/dedicated-hosts-overview.html

## AMI (Amazon machine Image)
- 서버 기본 설계도, 청사진 (운영체제, 애플리케이션 서버 등과 관련된 세부 내용)
- root volume: 운영체제와 애플리케이션 서버 또는 리눅스, 아파치, MySQL, PHP로 구성된 LAMP 스택, 기타 커스텀 애플리케이션 등 운영체제 위에서 실행되는 소프트웨어가 모두 포함
- launch permission: 누가 이 AMI로 인스턴스를 론칭할 수 있는가?
  - Public: 모든 AWS 계정 사용자
  - Explicit: 특정 AWS 계정 사용자
  - Implicit: 암묵적으로 승인 (?)

- block device mapping: 어떤 볼륨 타입을 부착할 것인가?
- 특정 리전에서는 론칭 인스턴스 갯수 제한 있음 -> 티켓으로 제한 해제 가능

### Instance Root Volume
- Amazon instance store-backed AMI: S3에서 론칭에 필요한 정보 가져와서 인스턴스 론칭
  - 인스턴스 부팅에 사용되는 이미지가 루트 볼륨 (인스턴스 스토어 볼륨)에 복사됨
  - instnace terminate시 볼륨 데이터도 같이 사라짐 (복구 불가)
  - 재부팅은 예외
  - 데이터 정기 백업 or 여러 AZ의 인스턴스에 분산 저장 필요

- Amazon EBS-backed AMI: EBS 스냅샷에서 론칭에 필요한 정보 가져와서 인스턴스 론칭
  - 인스턴스의 생명주기랑 EBS 데이터 생명주기랑은 별개 (영향 x)
  - 루트 볼륨을 인스턴스들에 떼었다 붙였다 가능
  - EBS-backed AMI를 권장

### AMI 선택하기
- 기존에 있는거 써도되고, 스스로 커스텀 AMI를 만들어 쓰거나 다른 사람들이 만들어 놓은 AMI (shared AMI)도 사용 가능
- 나만 쓰려면 private 속성으로 사용
- AMI 생성 시, EBS 볼륨 혹은 인스턴스 스토어로 백업 가능
- AMI는 리전 별 리소스. 다른 리전 공유 시, 복사 후 공유해야함
- AWS marketplace에서 AMI 사고 팔 수도 있음

### 가상화 방식
- [공식 문서](https://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/virtualization_types.html)
- HVM 쓸 것!!

#### HVM (하드웨어 가상화 머신) AMI
- VM 위에서 운영체제가 바로 실행 -> 운영체제에 하드웨어의 모든 설정 내용을 가상화 (완전한 가상화)
- bare-metal과 유사
- 모든 하드웨어 자원을 최대한 활용, 호스트 시스템의 하드웨어에 신속하게 접근 -> 높은 성능

#### PV AMI
- 게스트는 호스트의 하드웨어 위에서 실행됨 -> 완벽한 가상화 기능은 제공하지 못함 -> 강화 네트워킹, GPU 프로세싱 등의 기능 활용 불가
- 현 세대 인스턴스 타입들은 지원 x

## Instance Lifecycle
- 론칭
- 시작 및 정지 (EBS-backed 만 정지 가능)
- 재부팅
- 삭제
- 폐기

## Security Group
- 규칙
  - 프로토콜
  - 포트 범위
  - ICMP 타입과 코드
  - 소스 및 데스티네이션
- 특정 포트에 하나 이상의 규칙이 적용된 경우: 가장 관대한 규칙 적용

## Amazon Elastic Container Service (ECS)
- EC2 클러스터에서 도커 컨테이너를 관리할 수 있게 해주는 컨테이너 관리 서비스
- 가상화: 머신 자체를 파티셔닝 / 컨테이너: 프로세스를 격리
- 장점
  - 클러스터 관리 소프트웨어를 설치할 필요 없음
  - 어떤 규모의 클러스터도 쉽게 관리 가능
  - ECS를 이용해 내오류성 클러스터 아키텍처 설계
  - 클러스터 상태 관리 가능
  - 세밀한 컨테이너 통제 및 모니터링 가능
  - 거의 즉각적으로 하나의 컨테이너에서 수만개의 컨테이너로 확장 가능
  - 컨테이너의 배치 위치에 대한 의사결정 지원
  -  (CPU,메모리등) 리소스 가용성 정보 제공
  - EC2 AutoScaling을 이용해 언제든 클러스터에 새 리소스 추가 가능
  - Amazon Elastic Container Registry 외에 ELB, EBS, ENI, VPC, 머 M, CloudTrail 등 아마존의 다양한 서비스와 통합 사용 가능
