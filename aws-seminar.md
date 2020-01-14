# AWS 세미나



`VPC` : Virtual Private Cloud



* VPC 내 AZ(가용존) 존재

* AZ 내 IDC 여러개 존재



`AWS 에서 Default 는 가급적 지우지 마는 것을 권장`



* 개발, stage, 운영 별 VPC 별도 구성 혹은 하나의 VPC 내 여러 Subnet 분리
* **VPC 별도 구성 권장**



### Example

* vpc name : vpc-apne2-(dev, stg, prod)-(service 약칭)
  * vpc-apne2-dev
  * CIDR : VPC 가 가지는 IP 범위
    * 10.1.0.0/16
    * VPC IP 개수 : (2^16-4) 
  * 서울 RZ 내 IDC 하나 생성했다고 이해
  * DNS 호스트 이름 : 활성화 권장, 기본값은 비활성화
  * 라우팅 테이블(기본) : 서브넷 간 통신 규약
  * DHCP 옵션 세트 : 내부 리소스 IP 자동 할당
  * 네트워크 ACL : 서브넷 단위 Firewall
  * 시큐리티 그룹 : Instance 단위 FIrewall
    * 주로 시큐리티 그룹을 즐겨 사용
  * DNS 호스트 이름/편집

* how to comm outside
  * vpc 에 `internet gateway` attach
  * **인터넷 게이트웨이 생성 후, VPC Attach 필수**
* 구성도
  * 1 vpc
  * 4 subnet(all public)
  * 1 igw
  * 1 route table
* Network ACL
  * VPC ID | VPC Name
  * Default 는 Inbound, Outbound All 허용
  * **서브넷 생성 후, ACL 설정 필수**
* bastion host
  * 서브넷 내 ec2 생성 후 ssh 22 포트만 허용
  * ssh 전용 ec2가 포함된 subnet(`Bastion Host`) 생성 후, 이를 통해 다른 인프라에 접근 허용해야 함.
    * 요약하자면, 다른 호스트에 접근하기 위한 proxy subnet

* 4 subnet
  * 1 bastion host
  * 3 subnet
    * apache
      * bastion host -> apache
      * igw -> apache

* public vs private
  * routing 테이블이 igw 를 포함하는지 안하는지로 구분

* bastion host 생성
  * 가용존 선택
  * subnet-apne2c-dev-ssh
    * **naming rule 설정 중요**
    * vpc 는 여러 가용존을 포함하므로 subnet 명에 가용존까지 넣어줘야 한다
    * 서브넷은 가용존 영향을 받음
    * VPC 내 가용존을 여러개 두는 것을 권장(**다중 가용존**)
      * public cloud 가 장애가 빈번히 발생하므로
  * 1 region 내 max 5 vpc(증가 가능)
* 네트워크 트래픽을 줄여야 과금을 줄일 수 있음
* instance 에 대해서 azure 는 shutdown 시켜도 과금되지만, aws는 그렇지 않음
  * 같은 AZ 내에서 다른 Subnet 에 통신 시 과금이 안 될 수 있음(`상품에 따라 다름`)
  * 다른 AZ 내에서 Subnet 통신 시에는 과금됨

* 서브넷 생성
  * ipv4 cidr 블록은 VPC CIDR 를 범위 내에서 만들어야함.
    * vpc cidr : 10.1.0.0/16
    * ipv4 cidr : 10.1.0.0/24
    * 3번째 숫자가 적어질 수록 security 가 높아지게 cidr 블록을 설정
  * **VPC CIDR 블록 설정 시, 다른 VPC CIDR 블록과 겹치지 않도록**
* 라우팅 테이블
  * name : rt-apne2-dev-ssh
  * subnet 연결
* EC2 생성
  * management console - ec2
  * key pair 생성 -> **파일 다운로드 필수!!**
  * 하나의 키 페어로 모든 인스턴스에 적용하는 건 매우 위험
  * 원칙적으로 생성한 ec2 의 keypair 교체는 불가하나, 방법은 있다
  * 대시보드 - 인스턴스 시작
  * 인스턴스 유형
    * General Purpose 
    * 유형 앞에 t 가 붙는 건 개발용 혹은 테스트용
    * t 뒤 숫자는 세대를 의미(숫자가 클수록 최신)
  * 외부에 통신이 필요한 서비스는 **Public IP 자동 할당 활성화**
    * reboot 시, Public IP 은 고정되지만 Private IP 는 변경됨
    * 모니터링(CloudWatch) 는 활성화 권장



* Apache 서버 구축

  * 퍼블릭 서브넷 생성

    * subnet-apne2c-dev-web

  * 라우팅 테이블 생성

    * rt-apne2-dev-web

  * 시큐리티 그룹 생성

    * sg_apne2_dev_web

  * web 서버 인스턴스 생성

    * ec2-apne2c-dev-web
    * key pair 는 동일

  * web 인스턴스 설치 후

    * ```sh
      $ sudo yum update
      $ sudo yum install httpd
      $ sudo systemctl start httpd
      $ curl localhost
      ```

* S3(Simple Storage Service)

  * VPC 외부에 있음
  * S3 = Bucket
  * python 으로 구현된 오브젝트 스토리지 -> Swift
  * 버킷 생성
  * 이미지 업로드
  * 파일 접근 권한을 Public Access 로 변경



> 실습은 위에까지



* ELB
  * private ec2 웹 프로토콜 접속 시, ELB를 통해서 접근해야 함
  * 어플리케이션 load balancer는 L7까지 지원
  * 클래식 load balancer는 L7까지 지원
  * 1단계 로드밸런서 정의 시 어떤 서브넷 선택할지만 잘 정하면됨
    * VPC 내 구조도
      * ELB - 2개의 웹 서버와 연결됨(ELB -> Web)
      * 2개의 웹 서버는 각각 API 서버와 연결됨(Web -> ELB -> API)
  * 5단계 EC2 추가
    * Public Subnet 에 연결된 인스턴스 검색됨
    * EC2 에 ELB를 거치지 않고 들어오는 경우를 막는 방법
      * 기본적으로 ACL -> ELB -> EC2 로 들어오는 경로
      * ACL -> EC2 로 직접 들어오는 것도 고려
        * **Security Group**(EC2 인바운드 중 ELB를 통해서 들어오는 것만 수용)



* CloudFormation
  * Ansible
  * Terraform



* **NAT**
  * bastion host 로 web 서버에 접속하여 sudo yum update 시, 외부와 통신이 필요한 경우
  * NAT instance 혹은 NAT Gateway
  * 오로지 인터넷 게이트웨이를 통해 out 만 허용
  * AWS 정책상, 외부에서 NAT 접근 불가
  * Elastic IP 설정 가능(서버 재부팅하더라도 IP 주소 고정)
    * **유료**
* 네트워크 구성도
  * **ELB 와 Bastion Host를 제외한 나머지 Subnet은 Private 설정**
  * NAT를 통해서 Private Subnet 에서 외부 다운로드를 허용 가능하다
