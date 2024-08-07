# Step0. AWS 계정 생성
* AWS 계정이 없을 시, https://aws.amazon.com/free 에 접속하여 계정을 생성합니다.
  - 단, 이미 계정을 제공 받았을 경우 제공 받은 계정을 사용합니다.

# Step1. AWS Instance를 생성합니다.
* 단, 이미 인스턴스 를 제공 받았을 경우 제공 받은 인스턴스를 사용하고 이하 모든 Step을 생략 합니다.
* Zone은 상관없으며, Ubuntu 18.04이상 OS와 인스턴스 타입 t2.micro에서도 작동합니다. 단, 권장사항은 Ubuntu 24.04이며 인스턴스 타입이 t2.small 이며, Main Disk도 40G 입니다.
* Security Group Setting
  - Inbound : 22, 9411,8081

# Step2. Install Terraform And Ansible
* 아래 스크립트를 실행합니다.
```
sudo -i
curl https://raw.githubusercontent.com/Finfra/sreMsa/main/Lab1.Kubespray/Lab1.InstanceForTerraform/installOnEc2.sh|bash
```

# Step3. 설치 확인
```
terraform -version
ansible --version
```

# 생각해보기
- dockerhub container 의 image 를 사용해서 terraform 과 ansible 환경구성을 하는 건 어떨까, 또는 맞춤형 도커이미지를 만드는 건 어떨까 ?
```
- 업데이트 운영 관점에서의 평가
terraform container 는 hashcorp 가 공식적으로 올려주나, ansible container 는 오픈소스 컨테이너만 있음. 

유지보수 측면에서 ansible container 는 일정한 container 업데이트를 기대할 수 없음.

또한, 적용되는 환경에 docker engine 이 전제되야하고, container 의 '격리된 환경' 특성 상, 

iac script 실행을 위해 host 에 대한 볼륨 마운트 또는 docker cp 등 추가적인 절차와 명령어가 필요함.

이는 큰 진입장벽은 아니고, 결국 의존성의 악순환과 기술부채로 운영이 정지된다고 생각한다면, 

iac 도 container로 하는 것이 장기적으론 바람직하나, 레거시 상황상 가이드의 우선순위는 아니라고 볼 수 있음.

- 디스크와 데이터 운영 관점에서 평가
aws 클라우드벤더 사의 cli container까지 다운로드받는다고 가정할 때, 

aws cli container, ansible container, terraform container 를 모두 다운로드 하면 컨테이너 총 용량이 약 1G 정도 됨.

터미널 상으로 개별 다운로드 받는 것이 많아야 몇백메가라면, 컨테이너단위는 최소 몇백메가 단위로 증가하여 데이터를 조금 더 소모할 수도 있어보임.

이는 , aws free tier 로 운영할 것 아닌 이상, TB단위 데이터 운영관점에서는 충분히 기존 운영사이클 내에서 제어가능할 것으로 예상. 

또한 쉽게 지우고 다시 pull 받는 환경이라면 영향도는 없는 걸로 분류가능

결과적으로, dockerfile 연습 겸 iac-awscli image 를 사용예정
```