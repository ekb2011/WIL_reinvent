# 1. Transit Gateway

## 도입 전후 비교

* **Transit Gateway 도입 전**
![](https://media.amazonwebservices.com/blog/2018/tgw_before_1.png)
* VPC Peering을 통해 한 쌍의 VPC를 연결할 수 있었으나, 중앙 제어 및 관리가 불가능
* 각각의 VPC를 따로 관리해야 하는 Resource 소모
<br/><br/>
* **Transit Gateway 도입 후**
![](https://media.amazonwebservices.com/blog/2018/tgw_after_3.png)


## 기능

### 1. VPC 관리

* **VPC 간 트래픽 균등 분산**

* **Gateway 당 5,000개의 VPC 연결**

### 1.1 VPC (Virtual Private Cloud)
* **구성 예**
![](https://lh3.googleusercontent.com/6Yai9jKFdE5jXHsHUbRCzdBjlIcBwGt-0SWETQ0A4ARZ9oypOpto75QXqLkvCxKWtI97bn28HguU)

* **논리적으로 격리된 가상의 네트워크에서 클라우드 리소스를 운영**
* **독립된 서브넷, 라우팅 테이블 및 게이트웨이 구성**

* **구성요소**
```
    - Security Group
    - Subnet - VPC를 작은 단위로 나누어 개별 네트워크 형성. VPC 당 10개
    - Internet Gateway - Subnet이 외부와 연결되는 통로
    - Routing Table - 포워딩 테이블(입출력 포트에 관한 테이블) 및 최적 라우팅 정보를 나타낸 테이블
    - Router - 패킷의 위치를 추출하여, 위치에서의 최적 경로를 지정. 데이터 패킷을 해당 경로를 통해 전향
    - Private Network - 사설 네트워크를 정의, Public 망으로 트래픽이 전달되지 않음
    - Interface - 인스턴스를 네트워크에 연결하기 위한 장치
    - VPC Peering - 서로 다른 두 VPC를 연결
```
* **항목 별 Quota**

| **항목** | **최대값** |
| --- | --- |
| VPC | 3 |
| Subnet | VPC 당 10개 |
| Internet Gateway | 10개 |
| Floating IP | X |
| 라우팅 테이블 | VPC 당 10개 |
| 라우터 | 라우팅 테이블 당 10개 |
| Peering | X |
### 2. VPN 연결

* **온프레미스 네트워크와의 라우팅 전파 - BGP (Border Gateway Protocol) 활용**
``` 
BGP (Border Gateway Protocol)

- 자치시스템 상호 간에 적용되는 라우팅 프로토콜
- 무제한적인 순환을 피하기 위해 목적지까지 가는 경로 정보 제공
- 처음 BGP Session을 형성할 때 전체 라우팅 정보를 활용하고, 이후 변화가 있을때 이웃 라우터에게 갱신 정보를 알려줌
- 네트워크 변화가 없을 시 BGP Keep Alive Message를 60초 마다 교환
```

### 3. Routing
* **VPC와 VPN 간에 동적 및 정적 계층 3 라우팅을 지원, 패킷의 대상 IP 주소에 따라 다음 홉을 결정**
* **Amazon VPC와의 라우팅 전파: Amazon VPC CIDR (Classless Inter-Domain Routing)이 내부 API를 사용하여 Transit Gateway 라우팅 테이블로 전파**

### 4. Monitoring
* **CloudWatch, VPC Flow Log, CloudTrail을 활용한 로그 분석**
* **CloudWatch**
    * 요청이 Transit Gateway를 통해 전송될 때 VPC가 요청에 대한 지표를 60초 간격으로 수집
    * Byte, Packet의 In/Out과 Packet이 Drop된 갯수
* **VPC Flow Log**
    * VPC 네트워크에서 송수신되는 IP 트래픽 정보 수집
    * 전송된 패킷 수, 바이트 수
* **CloudTrail**
    * Transit Gateway의 API 호출을 감지

### 5. 관리
* **CLI, Management Console 등을 사용하여 Transit Gateway를 생성하고 관리**
* **VPC와 VPN 간에 주고받은 바이트 수, 패킷 수, 드롭 수와 같은 Amazon CloudWatch 지표를 제공**
* **VPC Flow Log를 사용하여 Transit Gateway 연결을 통해 전송되는 IP 트래픽에 대한 정보를 확보**

### 6. VPC 기능 상호 운용성

* **Transit Gateway에 연결된 VPC의 인스턴스에서 Transit Gateway에 연결된 다른 VPC에 접근 가능**


## 도입 시 이점

* **VPC 간의 상호 연결성 확보**
* **VPC를 중앙에서 관리하는 콘솔, 모니터링 서비스 지원**


## Transit Gateway's Quota

| **항목** | **최대값** |
| --- | --- |
| VPC 포함 VPN 등의 총 갯수 | 5,000 |
| 계정 당 한 Region에 생성할 수 있는 Transit gateway 갯수 | 5 |
| VPC에 연결할 수 있는 Transit Gateway의 갯수 | 5 |
| Transit Gateway 당 라우팅 테이블의 최대 갯수 | 20 |
| 라우팅 테이블 당 라우트 갯수 | 10,000 |
| VPN 연결 당 최대 대역폭 | 1.25Gbps |
| VPC 연결 당 최대 대역폭 | 50Gbps |
## 과금

* **시간당 Transit Gateway에 대한 연결 수와 Transit Gateway를 통과하는 트래픽의 양에 대해 요금이 부과**
* **VPC 계정 소유자는 Amazon VPC 또는 VPN이 Transit Gateway에 연결되는 각 시간에 대해 시간당 요금이 청구**
* **1시간 미만의 Transit Gateway 사용 시간은 1시간으로 청구**

| **지역** | **연결당 요금(USD)** | **처리된 데이터 GB당 요금(USD)** |
| --- | --- | --- |
| 서울 Region | 0.07 | 0.02 |




# 2. DynamoDB Transaction & On-Demand 

## DynamoDB

* **NoSQL 기반 비관계형 데이터베이스**
* **비관계형 데이터베이스 - 전형적인 테이블 형식의 스키마를 사용하지 않고, 데이터 형식의 요구 사항(Key-Value, JSON 등)에 맞게 저장소 모델을 사용**

### RDBMS vs NoSQL

|  | **관계형 데이터베이스** | **NoSQL** |
| --- | --- | --- |
| Workload | 트랜잭션 및 일관된 OLTP<br/>OLAP에 이상적 | 낮은 지연 시간<br/>반정형 데이터 분석 |
| Data Model | 정규화된 테이블 | Key-Value, JSON, Document 등 |
| 성능 | 쿼리, 인덱스 및 테이블 구조 최적화 필요 | H/W Cluster 크기, Network Latency Time, 애플리케이션 기능에 따라 |
| API | SQL 쿼리 | 객체 기반 API 통해 데이터 저장 및 검색 |
| 데이터 저장 및 처리 | 단일형 | 분산형 |
| 데이터 간의 관계 | 테이블 생성 시 정의 | 테이블 생성 시 정의 필요 X |
### NoSQL 데이터 모델
```
Table : Item들의 모임
Item : Attribute들의 모임
Attribute : Key - Value 방식
```
* **Key-Value**
    * Unique한 Key에 하나의 Value
    
    * Column Family - Key에 연결된 Value를 (Column, Value) 조합으로 여러 개의 필드 형성
   
* **Ordered Key-Value**
    * Column Family의 형태에서 Key를 기준으로 정렬되어 저장
* **Document Key-Value**
    * Key-Value에서 Value를 Document(JSON, XML, YAML 등)으로 저장
    * RDBMS의 Sorting, Joining, Grouping 가능
### 특징
* **신속, 단순한 배포 및 확장**
* **빠른 응답시간**
* **데이터 자동 복제**
* **보조 인덱스를 통한 빠른 조회**
* **비정형적인 데이터 저장에 유용**
* **기본 키**
    * 파티션 키 - 일반적인 기본키. 내부 해시 함수에 대한 입력으로 사용
    * 복합 기본키 - 파티션 키 및 정렬키로 구성. 유연한 데이터 쿼리
    * 정렬 키 - 물리적으로 상호 근접한 항목들을 정렬 키 값에 의한 정렬 순서로 저장 가능

## 기능

### 1. 캐시 지원

* **DAX (DynamoDB Accerlator)를 활용한 캐싱 서비스**
* **응답 시간 10배 절감**
* **Read 에 가장 빠른 응답 시간을 요구할 경우, Read 중심 서비스이지만 비용에 민감한 경우 등**
### 2. 보조 인덱스

* **대체 키와 함께 테이블의 속성 하위 집합을 포함하여 Query 작업을 지원하는 데이터 구조**
* **Query를 사용하여 인덱스에서 데이터를 가져올 수 있음**
* **한 테이블에 여러 보조 인덱스를 포함할 수 있어 서로 다른 여러 쿼리 패턴에 애플리케이션 액세스를 제공**

| **특성** | **글로벌 보조 인덱스** | **로컬 보조 인덱스** |
| --- | --- | --- |
| 스키마 | 단순 기본키(파티션 키) 혹은 복합 기본키(파티션 키 및 정렬 키) | 복합 기본키(파티션 키 및 정렬 키) |
| 온라인 인덱스 작업 | 추가 및 삭제 가능 | 추가 및 삭제 불가 |
| 쿼리 및 파티션 | 모든 파티션에서 전체 테이블 쿼리 | 단일 파티션 쿼리 |
| 프로비저닝된 처리량 소비 | 읽기 및 쓰기에 대한 자체 프로비저닝 처리량 설정 가능(인덱스 용량 단위) | 기본 테이블의 읽기 및 쓰기 용량 단위 소비 |

### 3. 스트림
* **기본키 속성을 사용하여 테이블의 데이터 항목에 발생한 모든 변경 정보를 캡처**
* **변경 사항에 대한 로그 데이터 24시간 보관, 액세스 가능**
* **스트림 Endpoint**
![](https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/images/streams-endpoints.png)
### 4. 트리거
* **Lambda 활용, 새로운 스트림 레코드가 감지될 때마다 Lambda 함수를 동기식으로 호출**
* **테이블의 데이터 수정에 응답하는 애플리케이션 구축**

### 5. 전역 테이블

* **다중 Region의 Master Database를 자동으로 배포**
* **전역 테이블로 설정한 테이블의 데이터가 변경될 때마다 다른 리전으로 자동 전파**
* **특정 Region의 장애가 발생했을 때 다른 Region에서 동일한 테이블로 Access 가능**

### 6. 특정 시점으로 복구

* **Incremental 백업(변경, 추가된 데이터만 백업)을 유지, 35일 이내 시점으로 복귀 가능**


### 7. (NEW) Transaction 지원 

`
Transaction : 
데이터베이스의 상태를 변화시키기 위해서 수행하는 작업의 단위
`



* **NoSQL의 Transaction 취약점 보완**
    * 비관계형 데이터베이스에서 지원하지 않는 여러 파티션과 테이블에서의 Transaction 가능
* **여러 항목에 대해 삽입, 삭제 또는 업데이트를 해야하는 어플리케이션 구축 가능**
* **TransactWriteItems - 테이블의 전제조건을 확인 후 Put, Update, Delete Operation 수행**

* **TransactGetItems - 테이블의 Write가 이루어지지 않고 있을때 복수의 GetItem Operation 수행 가능**

* **트랜잭션 당 10개의 Item, 4MB 데이터 제한, 리전 간 지원 불가**
* **특정 테이블에서 Transaction이 진행 중일 때 외부에서 항목이 변경되면 Transaction 취소 및 Exception 발생**
* **단일 Region DB 테이블에 대해서 우선 지원, 필요 시 글로벌 Region 테이블에서 활성화 가능**
    * 리전 별 복제는 비동기적으로 수행
    * Serializable Isolation 보장
`Serializable Isolation - 특정 항목에 대한 동시 접근을 차단하고 순차적으로 트렌젝션을 처리하는 것`
## 과금


### 1. Provisioned

* **기존 과금 정책**
* **월별로 프리 티어 초과분에 대해 추가금 부과**

```
프리 티어 기준
- 25GB 데이터 스토리지
- 2억 건의 요청
- 25건의 쓰기 용량 유닛과 25건의 읽기 용량 유닛
- 2백 50만 건의 스트림 요청
- 전역 테이블 배치 - 최대 두 곳의 리전
```
### 2. (NEW) On-Demand 

* **Capacity Planning 불필요**
    * 트래픽 예측이 어려운 신규 서비스, 짧은 지속시간이 산재된 서비스, 테이블 이용 평균이 최고점을 훨씬 밑도는 서비스 등의 비용 산정에 유리
    * R/W Pay-Per-Request
    * R/W Traffic이 없을 시 Data Storage에 대한 요금만 부과

## 도입 시 이점

* **데이터의 고속 처리**
* **데이터의 안정성**
    * Sharding - 같은 테이블 스키마를 가진 데이터를 다수의 데이터베이스에 범위 혹은 카테고리 별로 나누어 분산 저장
* **정해진 스키마 형태가 없어 유연하게 데이터 모델 변경 가능**
* **비관계형 데이터베이스의 활용 범위 확대**
    * 금융 트랜잭션, 주문 이행 및 관리 등


# 정리 및 기대효과


## 1. Transit Gateway

### Hub & Spoke
`Hub & Spoke 방식 - Transit Gateway가 Hub Site 역할을 수행하고, VPC는 Hub에 연결된 Spoke Site 역할을 수행`
* **복수 개의 VPC를 연결하고 관리 - 다수의 VPC를 보유한 서비스의 니즈 충족**
* **VPC Peering으로 불가능했던 VPC 간 상호 연결성 보장**
* **VPC 당 모니터링 서비스를 구축 불필요 - Workload 감소 및 운영 상 효율성 추구**

## 2. DynamoDB

### 비관계형 데이터베이스의 한계점 극복

* **Transaction을 지원 - NoSQL의 근본 취약점 해결**

### 과금 방식의 다양화

* **기존 과금 방식에 비해 간소화**
* **서비스, 비관계형 데이터베이스의 특징을 바탕으로 요금 상품 고안**