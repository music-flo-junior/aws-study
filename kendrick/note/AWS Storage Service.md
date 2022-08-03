# AWS 스토리지 서비스
## 세가지 스토리지 유형
1. 객체 스토리지
- S3, Glacier
- 데이터 (객체): 문서, 이미지, 비디오 등 비교적 단순한 구조에 메타데이터를 포함하고 있는 데이터 조각

2. 블록 스토리지
- EBS, EC2 Instance Store
- 데이터: 서버 인스턴스의 디스크 볼륨 형태로 제공되는 데이터

3. 파일 스토리지
- EFS
- 데이터: 서버 인스턴스에 파일 시스템 인터페이스 또는 파일 시스템 시멘틱스 방식으로 제공되는 데이터

## S3
### 특징
- 파일 시스템 x. 단순 네임스페이스 기준으로 저장

### 장점
- 간편성
  - 웹 콘솔, 모바일 앱, REST API, SDK, AWS CLI
- 확장성
- 신뢰성
  - 자동 반복 저장 - 다수의 데이터 센터, 다수의 스토리지 기기
- 보안성
  - SSL을 통한 데이터 전송, IAM 접근 권한 설정
- 고성능
  - Multipart Upload 기능, CloudFront 연계 기능
- 가용성
- 저비용
- 관리 용이성
- 연계성

### 활용 방법
- 데이터 백업
- 데이터 저장 장치 대체
- 정적 웹사이트 호스팅
- 애플리케이션 호스팅
- 재난 복구
- 콘텐츠 배포
- 데이터 레이크
- 프라이빗 저장소

### 기본 개념
- http://newringtones.s3.amazonaws.com/ringtone.mp3
  - `newringstones`: bucket name (유일무이)
  - `ringtone.mp3`: file name
- http://s3.amazonaws.com/2017-02/pictures/photol.gif
  - `2017-02/pictures/photo1.gif`: 객체 키

- REST API, SDK 사용해서 S3기반 애플리케이션 개발 가능
- AWS CLI로 관리

### 데이터 일관성 모델
- 웹 기반의 데이터 저장소 (한 번 기록하고 여러 번 읽음이 목적)
- 구성:
  - AZ
  - LB
  - Web Server
  - Storage
- 플로우:
  - 객체 작성 -> LB -> Web Server API -> 다수의 AZ, 다수의 스토리지 중복 저장 -> 인덱싱 작업 (중복 저장)
  - `S3-0ne Zone InfrequentAccess`: 단일 AZ 저장
  - LB, Web Server 다운 시: 중복 구현된 또 다른 LB, Web Server에 요청
  - 스토리지 유닛, 인덱싱 스토리지 다운 시: 중복 구현한 다른 스토리지에 저장
  - AZ 다운 시 (Failover): 다른 AZ를 통해 서비스 제공

- 판독 일관성 (read-after-write consistent): 새 객체는 작성 시 (POST) 동기적으로 다수의 클라우드 설비에 저장
  - 객체 생성 (write) 후 객체가 완전히 확산됨을 보장하므로 read시 일관성을 보장받을 수 있다
- 종국적 일관성 (eventually consistent): 기존 데이터 (객체) 동일 리젼 내 자동 복제
  - 기존 데이터 수정 시, 변경 된 내용을 즉각 확인 불가할 수도 있지만, 종국적으로는 (결국에는) 일관성을 갖추게 될 것

- 단일 키에 대한 업데이트(PUT)는 Atomic
- Object Locking은 애플리케이션단에서 구현가능
- lagging 현상 해소법: 애플리케이션에 의한 재접속, DynamoDB에 메타데이터 관리 + S3 실제 컨텐츠 관리

### 성능
- 초당 100회 이상의 PUT/LIST/DELETE 혹은 초당 300회 이상의 GET -> 워크로드 분산 고려 필요 -> S3 버킷 자동 파티셔닝

- `key prefix`에 따라 파티셔닝 - 시작 단어로 묶음

#### 키 이름 문자열 역순 배열

- 성능 향상 전
```
applicationid/5213332112/log.text
applicationid/5213332112/error.text
applicationid/5213332113/log.text
applicationid/5213332113/error.text
applicationid/5213332114/log.text
applicationid/5213332114/error.text
applicationid/5213332115/log.text
applicationid/5213332115/error.text

partition: applicationid/5
```

- 성능 향상 후

```
applicationid/2112333125/log.text
applicationid/2112333125/error.text
applicationid/3112333125/log.text
applicationid/3112333125/error.text
applicationid/4112333125/log.text
applicationid/4112333125/error.text
applicationid/5112333125/log.text
applicationid/5112333125/error.text

partition: applicationid/2, applicationid/3, applicationid/4, applicationid/5
```

#### 키 이름에 Hex Hash prefix 추가

- 성능 향상 후

```
키 값에 MD5 해시 알고리즘 -> 128비트 문자열 생성 -> key prefix로 할당

applicationid/112a5213332112/log.text
applicationid/c9125213332112/error.text
applicationid/2a825213332113/log.text
applicationid/7a2d5213332113/error.text
applicationid/c3dd5213332114/log.text
applicationid/8ao95213332114/error.text
applicationid/z91d5213332115/log.text
applicationid/auw85213332115/error.text
```

- 주의할 점: 해시 알고리즘의 랜덤 속성. 객체가 너무 많은 경우 파티션키가 과도하게 생성 될 수 있음 -> 2~3개의 문자열로 충분

### 암호화
- 데이터 전송 시 암호화
- 저장된 데이터 암호화
- HTTPS / SSL 암호화 종단점에서의 S3 사용: 자동으로 데이터 업로드, 다운로드, 전송 중 암호화 상태 유지
- S3 암호화 클라이언트 사용: 데이터 전송 시 암호화 상태 유지. 객체 (데이터)마다 1회성 랜덤 CEK 사용
- Server Side Encryption (SSE) 사용: 저장된 데이터의 암호화. AES 256-비트 대칭키 사용
  - Amazon S3 키 매니지먼트 기반의 SSE(SSE-SE)
  - 고객 자체 생성 키 기반의 SSE(SSE-C)
  - AWS KMS 기반의 SSE(SSE-KMS)

### Access Control
- S3에 대한 접근 권한을 통제

#### IAM 정책
- IAM을 통한 접근 권한 정책
- ARN (Amazon Resource Name): AWS 리소스를 위한 유일무이한 이름
```
arn:partition:service:region:account-id:resource arn:partition:service:region:account-id:resourcetype/resource

e.g.
arn:aws:s3:::bucketname
arn:aws:s3:::bucket_name/key_name
arn:aws:s3:::my_bucket_forawsbook
arn:aws:s3:::my_bucket_forawsbook/chapterl.doc
arn:aws:s3:::my_bucket_forawsbook/*
arn:aws:s3:::my_bucket_forawsbook/images/*
```

- 정책 예시
```
"Version";"2017-06-07",
"Statement";[
    {
      # 정책의 효과 Allow / Deny
      "Effect": "Allow

      # 정책을 통해 승인 또는 거절할 동작
      "Action": [
        "s3:PutObject",
        "s3:GetObject"
      ]

      # 정책이 적용될 aws resource
      "Resource":"arn:aws:s3:::my_bucket_forawsbook/${aws:username}/*"
    }
  ]
}
```

#### Bucket 정책
- 버킷 레벨에서 생성한 정책
- 예시
```
{
  "Version": "2017-06-07",
  "Statement":[
    {
      "Sid": "AddPerm",
      "Effect": "Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::staticwebsite/*"]
    }
  ]
}
```

- 가능한 것들: 여러 개의 계정에 한꺼번에 접근을 허용, MFA 인증을 반드시 거치도록 설정, CloudFront를 통해서만 접근 가능하도록 설정, 특정 HTTP 참조 기관의 트래픽을 거부하도록 설정, 객체 업로드에 대해서 교차 계정 승인을 거치도록 설정

#### Access Control List (ACL)
- bucket과 그 속의 객체는 ACL과 연동
- 넓은 범위의 제어에 초점 (접근 승인을 한 곳, 접근 승인을 받은 곳 수준의 구분)

### S3 Security - Best Practice
- 버킷에 대한 퍼블릭 엑세스 허용 x
  - Amazon S3 Block Public Access 기능 이용 가능
- `최소한의 접근 권한`전략 사용
- `Multifactor Authentication (MFA)` 시스템을 이용
- 버킷에 대한 감사를 수행
- IAM role 활용

### S3 Storage Class
- 다양한 상황에 대응할 수 있도록 다양한 storage class 제공

- Amazon S3 Standard
- Amazon S3 Standard IA (Infrequent Access)
  - 비교적 접근 빈도가 낮은 데이터 저장 용
- Amazon S3 One Zone-IA
  - 낮은 빈도 + 접근 속도
  - 단일 AZ에만 저장
  - Standard IA보다 20% 저렴
  - 객체 레벨에서 설정 - Standard 및 Standard IA와 동일한 버킷에 존재 가능
- Amazon S3 Intelligent-Tiering
  - 객체 접근 빈도에 따라 해당 객체의 티어를 자동으로 이동시킴
- Amazon S3 Glacier
  - 데이터 장기 보관 목적
- Amazon S3 Glacier Deep Archive
  - 7년 ~ 10년간 보관, 연간 한 두번정도 이용 하는 데이터 장기 보관 목적

### 객체 Versioning
- 파일 보존, 인출, 백업 (복원)

### 객체 라이프 사이클 관리
- 라이프 사이클 매니지먼트 활용 -> 성능 극대화, 비용 절감 가능
- 라이프 사이클 정책 목적
  - 파일 이동: 서로 다른 클래스 간에 객체를 이동시키는 규칙 추가
  - 파일 소멸: 객체가 소멸된 후의 사항을 정의

### S3 복제
- Cross Region Replication (CRR)
- Single Region Replication (SRR)

### S3 정적 웹사이트 호스팅

### Amazon S3 Glacier
- 데이터 아카이브 및 장기 보관 백업을 위한 저비용 클라우드 스토리지
- 낮은 비용! (1테라당 월 1달러)
- 활용
  - 자기 테이프 스토리지 대체
  - 헬스케어/생명과학/과학 데이터 저장
  - 미디어 자산 아키이빙/디지털 보관
  - 규제 정책에 따른 아키이빙/장기 백업 보관
- 개별 파일업로드보다는 여러개의 데이터를 압축해서 아카이브를 구성하는 편이 더 저렴
- write-once (수정 불가)
  - 수정하려면 재업로드
- 100MB 이상의 아카이브 업로드 시, 더 작은 단위로 세분화해 전송하는 multipart 업로드 기능 사용
- Vault: 데이터를 위한 금고. 여러개의 아카이브로 하나의 그룹 생성 후, 하나의 볼트에 넣을 수 있음. S3 Bucket 개념
- Valut Lock
- Vault Inventory (Archive Index)는 24시간 주기로 갱신

#### 접속
- API / SDK
- Amazon S3 라이프 사이클 통합 서비스
- AWS 서드 파티 도구와 게이트웨이

#### 파일 업로드
- 일반 인터넷
- AWS Direct Connect (기업 데이터 센터)
- AWS Snowball (데이터 양이 방대한 경우)

#### 파일 가져오기
- Standard
  - 저비용
  - 3~5 hours, $0.01/GB
- Expedited
  - 소수 아카이브, 일시적, 긴급
  - 1~5 minutes, $0.03/GB
- Bulk
  - 페타바이트급 데이터
  - 5~12 hours, $0.0025/GB

- Retrievla Job 제출 -> Job 수행 -> Job 완료 & 알림 -> 다운로드

### Amazon Elastic Block Store (EBS)
- EC2 Instance를 위한 Persistent Storage
- 서버를 위한 하드드라이브와 같은 기능 수행
- Multi-Attach 옵션의 EBS 볼륨을 사용하면, 다수의 EC2 instance에 하나의 EBS 볼륨 부착 가능 (하나의 Provisioned IOPS SSD (io1) 볼륨을 동일 AZ에 있는 최대 16개의 Nitro기반 Intsance에 추가 가능)
- 서로 다른 AZ간에는 EBS 볼륨 분리/부착 불가
- 부트 파티션으로 활용 가능
- 스냅샷을 지속적으로 작성, s3에 저장
  - 다수의 AZ에서 자동 복제 기능
  - 또다른 EBS 볼륨 생성을 위한 시작점
  - 협업에 용이
  - 재난 복구, 데이터 센터 마이그레이션, 지역 확장

#### 특징
- 영구 스토리지
- 범용성
- 고가용성 및 고신뢰성
- 암호화 지원
- 다양한 저장 용량
- 사용 편의성
- 실패 대응성

#### 주요 서비스
##### Amazon EC2 Instance Store
- 인스턴스의 로컬 스토리지. 해당 인스턴스만 사용 가능
- 인스턴스 셧다운시 저장된 내용 사라짐

##### Amazon EBS 볼륨
- Amazon EBS Elastic 볼륨: 동적인 용량 증대, 성능 튜닝, 성능 저하 없이 기존의 볼륨을 변경가능한 EBS의 특징

##### Amazon EBS SSD-기반 볼륨
1. 범용 SSD (gp2)
  - 다양한 수준의 트랜잭션 데이터를 처리하기 위한 가격 및 성능의 균형
  - 밀리초 단위의 전송 지연만
  - 1TB 미만 -> IO Burst Bucket 사용 가능: IO Credit 개념 적용. 상황에 따라 유연하게 성능 조절 가능
2. Provision IOPS SSD (io1)
  - 전송 지연 (IO 성능)에 민감한 트랜잭션 워크로드 처리를 위한 고성능
  - 성능 예측 가능성 및 일관성 보장
  - 볼륨:Provision IOPS = 1:50 (e.g. 100GB 볼륨 -> 5000 IOPS 제공)


##### Amazon EBS HDD-기반 볼륨
- IOPS보다는 Throughput에 초점을 맞춘 볼륨 타입
- 성능대비 가격 측면에서는 SSD 기반 볼륨이 Random IO 워크로드 처리에 유리

1. 처리 성능 최적화 HDD(st1)
  - 높은 빈도의 입출력 및 처리 성능
  - 높은 Throughput 워크로드, Sequential IO에 적합
  - Burst Bucket 가능
  - e.g. 데이터 웨어하우스, ETL, 로그 프로세싱, 맵리듀스
2. 콜드 HDD(sc1)
  - 낮은 빈도의 입출력 및 저렴한 비용
  - 중요성이 높지 않은 콜드 데이터 워크로드, 접근 빈도가 낮은 데이터 처리에 적합

### Amazon Elastic File System (EFS)
- EC2 인스턴스를 위한 파일 시스템 인터페이스 및 파일 시스템 시맨틱 환경을 제공
- 공유 파일 시스템: 다수의 인스턴스를 위한 공통 파일 시스템 역할

#### 특징
- 완전관리형
- 파일 시스템 액세스 시맨틱
- 파일 시스템 인터페이스
- 공유 파일 시스템
- 민첩성 및 확장성
- 고성능
- 고가용성 및 고신뢰성
- 요약: 고신뢰성, 고가용성 기반위에 간편성, 민첩성, 확장성을 구현한 파일 시스템

#### 활용
- 사용 절차
  1. 파일 시스템 생성
  2. 각 AZ에서 파일 시스템을 통해 접근하려는 마운트 타깃 생성
  3. EC2 Instance에서 EFS 마운트의 DNS 이름으로 마운트 명령을 실행
  4. 사용

#### 성능 모델
- 범용성 모델
- Max I/O 모드

### On-Premise X AWS
- 10TB 이상의 데이터를 데이터 센터에서 전송하려는 경우, AWS와 On-premise storage를 통합하는 방안을 생각해 볼 수 있다

#### AWS Storage GateWay (SGW)
- 기존 환경에 가상머신 형태로 배포 (Storage GateWay)
- 이를 AWS와 연결하여 사용
- 3가지 스토리지 인터페이스
  1. 파일 게이트웨이
    - 온프레미스 <-> NFS 마운트 포인트 <-> S3 버킷
  2. 볼륨 게이트웨이
    - iSCSI 블록 프로토콜 기반 디스크 볼륨 제공
    - Cached mode: 프라이머리 데이터는 S3에 저장, 자주 접근하는 데이터는 로컬에 저장해 신속하게 참조
    - Stored mode: 전체 데이터는 로컬에 저장, 비동기적인 백업 데이터만 S3에 저장
  3. 테이프 게이트웨이
    - Virtual Tape Library (VTL) 제공
    - 가상 테이프는 S3에 저장
    - 장기 보관 시, S3 Glacier로 이전 가능

#### AWS Snowball과 AWS
- Snowball: Amazon 스토리지 디바이스를 이용한 페타바이트 규모의 데이터 이전을 위한 import/export 도구 (외장 하드같은 느낌)
- Snowball에 이송하기 전 모든 데이터는 256-bit GSM으로 암호화
- 온프레미스 -> snowball device -> AWS 시설 -> S3
- Snowbal Edge: Snowball 보다 더 많은 100T의 데이터 복제 가능
  - Snowball Edge Compute Optimized
  - Snowball Edge Storage Optimized

#### AWS Snowmobile
- 엑사바이트 규모의 데이터 이전 서비스. 찐 대규모 데이터 import/export
- 최대 100PB까지 데이터 이전 가능
- AWS 전담 직원 호출 가능
