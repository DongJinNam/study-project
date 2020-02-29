# AWS 활용 QnA 셀프 게시판

### No default VPC for this user

* Docker hub에 있는 이미지를 AWS 에서 ElasticBeanStack 을 통해 기동 시 발생한 에러
* 기본 VPC 가 설정되어 있지 않아 발생
* 아래와 같이 설정
    * [Link](https://aws.amazon.com/ko/premiumsupport/knowledge-center/cloudformation-cloudformer-default-vpc/)
    * 참고로 템플릿 선택 시, [Link](https://github.com/awslabs/aws-support-tools/blob/master/CloudFormation/CloudFormer/CloudFormer-Existing-VPC.json) 참고 필요
