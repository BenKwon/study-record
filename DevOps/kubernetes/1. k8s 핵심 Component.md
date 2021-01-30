
 이전에 쿠버네티스에 관련하여 개인적으로 공부를 했었지만 정리를 안해두어   
나중에 봤을때 개념이 머릿속에서 떠오르도록 간단하게  정리해놓으려고 한다

> ###  서비스와 인그레스(Service and Ingress)

- 워커 노드에는 컨테이너를 품고 있는 Abstaction인 pod들 단위로 구성되어있는데  
이 pod마다 ip가 할당되게 된다. 하지만 일반적으로 이러한 pod들은 crash가 될경우 새로운 pod이 생성되어   
대체되게 되는데 이 경우 ip가 바뀌게 된다. 그래서 만약 internal 적으로 다른 pod에 있는 애플리케이션과  
의존성을 띄고있던 애플리케이션의 경우 오류가 나게된다.  

이를 위해서 Service라는 컴포넌트가 있다. 이는 각각의 pod에 붙는 permanet한 IP 주소이다.  
이제 부터 이 Service에 할당된 IP를 통하여 서로를 의존하게 된다. 
Service를 생성할때 External 과 Internal 두가지중에 하나로 할 수 있는데 전자의 경우 외부에서 
접근이 가능한 모드이다. 즉 해당 팟이 속한 노드의 ip:{팟 서비스 port}로 외부에서 접속이 가능하게 한다. 
후자 Internal의 경우 DB서버와 같이 외부에서 참조가 되면 위험한 서비스의 경우에 사용된다. 

하지만 일반적으로 외부 사용자 즉 일반 사용자들이 서비스를 이용하기 위해서는 Ingress가 사용된다. 
kubernetes의 Ingress는 외부에서 쿠버네티스 클러스터 내부로 들어오는 네트워크 요청   
Ingress 트래픽을 어떻게 처리할지 정의한다. 쉽게 말하자면, Ingress는 외부에서 쿠버네티스에서 실행 중 
인 Deployment와 Service에 접근하기 위한, 일종의 관문 (Gateway) 같은 역할을 담당한다.



#### - 정리
팟끼리 통신할땐 팟에 attached된 각각의 Service Object를 이용하고
외부 트래픽을 적절한 Deployment와 Service에 포워딩하기 위해서는 Ingress. 
(NodePort, ExternalIP 라는 Ingress 대체가능한 것들이 있지만 Ingress를 쓰는게 좋다. 이유는 쿠버네티스를 전체적으로 훑고 깊게 알아봐야겠다.)

> ###  ConfigMap 과 Secrets

- ConfigMap은 애플리케이션이 동작할때 필요한 환경값들을 Key Value형식으로 쿠버네티스에 저장해 놓는 곳이다.
예를들어 현업에서 서비스 테스트환경과 실제 배포환경은 다를 것이고 이에 따라서 DB URL이나 DB PWD는 다를 것이다.
물론 코드내에서 해당 값들을 직접 입력하고 배포하면 좋겠지만 ... 매번 이렇게 이미지를 빌드하고 배포해야하긴 번거롭다.
따라서 ConfigMap에 값만 바꿔주면 알아서 해당 코드내에 유동적으로 바뀌어서 적용된다고 한다.

- Secrets는 ConfigMap과 비슷한 역할을 하나 암호화 되고 민감한 정보(패스워드 등)을 위해서 사용된다


> ###  Volume
k8s는 데이터 persistance를 지원해주지 않아서 사용자가 알아서 해야한다.

> ###  Deployment
일반적으로 k8s를 사용하는 이유중 하나인 지속적인 서비스제공를 구현하기 위해
같은 pod을 여러개 복제 해놓는다. 그 pod들은 같은 Service가 붙여지고 이 Service가 요청에 대해서 Load Blancer역할을 한다.
이전에 pod을 container위에 있는 abstraction이라고 했는데 deployment는 pod위에 있는 또다른 abstraction이다.
일반적으로 사용자는 pod대신 deployment를 활용하게 되며 여기서 pod에 관련한 다양한 설정을 한다 (ex 복제 팟 개수 등등)
pod이 여러개가 되면 팟 1개가 죽어도 다른 팟들이 대체를 해주기 때문에 애플리케이션 이용자 측면에서는 끊기는 않는 서비스를 이용가능하게 된다.

