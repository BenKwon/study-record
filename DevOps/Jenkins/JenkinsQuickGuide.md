# 젠킨스 빠른 사용법
> 젠킨스를 설치하고 깃 허브와 연동 및 도커 허브 및 쿠버네티스 클러스터 배포등 CI/CD파이프라인을 구축하기 위한 기본 젠킨스 빠른 사용법.  
나중에 기억안날때 보려고 정리해놓으려고 한다.

## 젠킨스 설치
#### 1. 자바 설치
```sh
sudo apt update
sudo apt install openjdk-11-jdk
java --version
```
#### 2. 젠킨스 설치
```sh
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

#### 3. 젠킨스 시작
```sh
sudo systemctl start jenkins 
sudo systemctl status jenkins #가동 확인
```

## 젠킨스 대쉬보드 접속
> 젠킨스는 8080포트를 기본으로 서버가 열리므로 http://서버주소:8080 을 통하여 접속한다.   접속하게 되면 플러그인을 설치하고 아이디와 패스워드를  설정하면 대쉬보드 메인으로 넘어온다.

## 젠킨스 Slave 
> 젠킨스는 작업을 기본적으로는 Master Agent에서만 수행한다. 하지만 작업 부하를 분산시키위해 젠킨스 slave를 사용하기도 한다.  
나는 젠킨스 slave를 k8s 클러스터와 쉽게 연동하기위해 사용하기로 했다.
#### 1. 자바 설치 & 젠킨스 설치
- 기본적으로 젠킨스 slave에도 젠킨스가 설치되어있어야 하므로 젠킨스 설치를 진행한다.

#### 2. 젠킨스 신규 노드 추가
1. 젠킨스 마스터 노드의 대쉬보드에서 Jenkins 관리 -> 노드 관리 -> 신규 노드를 차례대로 들어간후 노드명을 지정하고   Permanent Agent를 선택한후 생성한다.
![image](https://user-images.githubusercontent.com/22045187/108360372-77e08200-7234-11eb-87fd-70c4d26b4796.png)

2. Remote root direcotry에  /home/ubuntu/jenkins 를 적어주고 Custom WorkDir Path에는 /home/ubuntu/jenkins를 적어준다. 이 경로는 slave 노드의 기본 작업 경로가 된다.
Remote root directory와 Custom WorkDir Path가 다르게 설정될 경우 Custom WorkDir Path로 기본 작업 경로가 될 것 같다.
![image](https://user-images.githubusercontent.com/22045187/108360851-110f9880-7235-11eb-8f93-cf07c5ec0ece.png)

3. 생성된 노드를 클릭하면 Java Web Start is not available for the JVM version running Jenkins 라는 문구가 뜰 경우 command line을 이용하여 slave node를  
등록해야 한다. 
```java
java -jar agent.jar -jnlpUrl http://my-master-ip:8080/computer/slave-1/slave-agent.jnlp -secret 1f5cdd17e753cbe82910e106b60a97b2afe0133b70e90373f3a73e0be93aebe2 -workDir "/home/ubuntu/jenkins"
```
위와 같은 형식의 커맨드 라인을 보여줄텐데 agent.jar을 다운해서 slave 노드에서 다운로드된 경로에서 해당 커맨드 라인을 실행하면 slave node로 등록된다.

## 3. FreeStyle Project에서의 노드 선택
> 이제 slave node를 생성했으니 내가 진행할 job이 어느 노드에서 실행되게 할지 선택 가능하다. 물론 pipeline과 같이 스크립트를 통해서도 노드를 선택할 수 있으나 여기서는  
일반 job에서의 등록 방법만 적어놓겠다.
1.FreeStyle Prjocet를 생성한다.
2.해당 프로젝트 설정에서 노드를 지정한다. 
![image](https://user-images.githubusercontent.com/22045187/108361383-bdea1580-7235-11eb-996e-6c29475e478b.png)
위 사진에서 파란색 부분에 생성해둔 slave node의 이름을 적으면 된다.
