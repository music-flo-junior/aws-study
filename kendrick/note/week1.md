# 기초 개념

## Virtualization 

- Emulator
  - VMware, Workstation, Virtual PC
- Full Virtualization
  - Hardware Virtual Machine (HVM)
  - CPU's Hypervisor
- Paravirtualization (PV)
  - Xen
  
## Region & Availability Zone (AZ)
- Region > AZ (1:N)
- 물리적(지리적)으로 분산 되어있음
- Fast Network, High Availability

## Edge Location
- CloudFront의 Cache Server (POP)

# Elastic Compute Cloud (EC2)
## Instance Type
- 범용: M1~, M3~
- 컴퓨팅 최적화: C~
- GPU 인스턴스: G~
- 메모리 최적화: M2~, CR1~
- 스토리지 최적화: H~, I~
- 마이크로 인스턴스

## 구매 옵션
- On Demand Instance
- Spot Instance
- Reserved Instance

## Elastic Block Store (EBS)
- EC2 인스턴스에 장착하여 사용할 수 있는 가상 저장 장치
- Block Device: 일정한 Block 단위로 읽고 쓰는 저장 장치
  - e.g. 자기테이프, 프로피디스크, 하드디스크, 광학디스크, SSD

### 기본 개념
- Volume
- Image (Amazon Machine Image - AMI)
- Snapshot
- IOPS (Input/Output Operation Per Second)

### 사용법
1. EBS 볼륨 생성
2. EBS 볼륨 장착 (attach)
3. EBS 볼륨 포맷
4. EBS 볼륨 마운트
5. EBS 볼륨 제거 (unmount & detach)

### EBS Snapshot
#### 목적
- 백업
- Custom AMI
  - Linux의 경우 Kernel ID 설정 주의!  
- 다른 Region, AZ로 볼륨 이동(복사)

## ETC
- Placement Group
- Bundle Instance (instance store AMI)
- Bundle Tasks
- Network Interfaces (Elastic Network Interface)
