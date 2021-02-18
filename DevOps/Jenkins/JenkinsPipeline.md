# JenkinsPipeline
> Build Test, Contract Test, DockerHub Pusy, deploy on k8s cluster가 포함된 파이프라인을 구현하려고 한다.


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
      stage('Build') { 
          build 'Test'
      }
      stage('Push Image') { 
          build 'docker-push'
      }
      stage('Deploy') { 
          build 'deploy-k8s'
      }
  }
```
