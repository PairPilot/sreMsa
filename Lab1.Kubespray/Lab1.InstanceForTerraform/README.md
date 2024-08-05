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
- dockerhub container 의 image 를 사용해서 환경구성을 하는 건 어떨까, 또는 맞춤형 도커이미지를 만드는 건 어떨까 ?
```
terraform 은 hashcorp 가 공식적으로 올려주나, ansible 은 오픈소스 컨테이너만 있음.
유지보수 측면에서 ansible이 공식컨테이너가 없고 공식가이드 상에도 따로 container 기반 설치를 설명하지 않음.

아마도,ansible을 활용하는 환경에 docker engine 이 전제될 확률이 낮기 때문으로 예상됨. 
또한, 클라우드벤더 사의 cli container까지 다운로드받는다고 할 때, aws cli container 기준, 다운로드된 컨테이너 총 용량이 약 1G 정도 됨.
터미널 상으로 개별 다운로드 받는 것이 많아야 몇백메가라면, 컨테이너단위는 최소 몇백메가 단위로 증가할 것으로 보임.

환경선택이 자유로운 경우 1회성으론 aws cli container 기반 이미지로 terraform, ansible 을 설치한 docker container 를 활용할 수 있지만,
그렇지 않은 경우 도커엔진이 기본이고, 용량이 충분한 환경을 전제해야하기 때문에 1순위 권고가 될 수 없어보임.

개인적으로는 이미 만든 iac-awscli image 를 사용해보지만, 기본적인 활용방안은 3가지 툴들의 공식가이드 내 공통 방법으로 설명하는 것이 바람직할 것으로 예상됨.
```