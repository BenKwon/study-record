# JenkinsPipeline
> Build Test, Contract Test, DockerHub Pusy, deploy on k8s cluster가 포함된 파이프라인을 구현하려고 한다.
파이프라인의 Scripted 문법을 이용해서 자유롭게 파이프라인을 구성할 수 있다. Scripted 문법은 Groovy 문법을 사용한다.

## 문법
- node : Scripted 파이프라인을 실행할 젠킨스 에이전트
- dir : 명령을 수행할 디렉토리 설정
- stage : 파이프라인의 각 단계를 선언
- git : Git에서 프로젝트를 Clone
- sh : 명령어 실행
- def : Groovt 변수 혹은 함수 선언 (JS의 var와 같은 개념으로 이해하면 좋음)

## 기본 pipeline 사용법
1. 새로운 item을 Pipeline으로 하나 생성한다.
2. 새로운 파이프라인에 대한 설정창이 뜨면 아래로 스크롤하면 Script를 입력하는곳이 나온다.
![image](https://user-images.githubusercontent.com/22045187/108361954-787a1800-7236-11eb-8643-2063e963f0cc.png)
3. 위 사진에서 자주색으로 색칠된 Pipeline Syntax를 누른다.
4. 그러면 Snippet Generator를 이용할수가있는데 필요한 Step을 찾아서 참고한다.
![image](https://user-images.githubusercontent.com/22045187/108362320-efafac00-7236-11eb-8bf6-fd3d2a7bcacc.png)

**스크립트 예시**
```script
 node('에이전트 노드 명') {  
      def example_var = "Hello World"
      stage('clone'){
          git 'https://깃레포지터리주소'
      }
      dir('some_dir'){
         sh 'ls -al'
      }
      stage('Build') { 
         // build 'Test'
      }
      stage('Push Image') { 
         // build 'docker-push'
      }
      stage('Deploy') { 
        //  build 'deploy-k8s'
      }
  }
```
