
## namespace 
> 클러스터내의 가상클러스터라고 보면 편하며  
기본적으로 4개의 네임스페이스가 생성된다  
1. kubernetes dashword ; minikube에만 생성되는거
2. kube-system : 여기는 수정하거나 생성등 건들지말것(system process master and kubectl processes)
3. kube-public : publicely accesible data (configmap) kubectl cluster-info 명ㄹ여어를 쳤을때 이러한 정보들이 여기서 나오나봄
4. kunr-node-lease :쿠버네티스 노드에서 보내는 하트비트는 노드의 가용성을 결정하는데 도움이 된다.  
하트비트의 두 가지 형태는 NodeStatus 와 리스(Lease) 오브젝트이다. 각 노드에는 kube-node-lease 라는 네임스페이스 에 관련된 리스 오브젝트가 있다. 리스는 경량 리소스로, 클러스터가 확장될 때 노드의 하트비트 성능을 향상 시킨다.

5.default
처음에 자원을 생성할때 사용되는 namespae 

**네임스페이스생성**  
kubectl create namespace name;  

namespace를 configrutaion 파일로 관리하는것이 좋다  

![image](https://user-images.githubusercontent.com/22045187/107508921-09247880-6be5-11eb-8807-63862097c941.png)  
1. 큰 프로젝트에서는 이렇게 용도별로 네임스페이스를 나눠서 활용하면 관리하기 편하다.  
단 공식적으로 작은프로젝트에는 권장되지않는다.  
2. 2팀이 잇다고하자 같은 클러스터에 1팀은 deploy aplloication을 한다고 하자. (특정 configurtaion이 있다)  
또다른팀은 같은 이름의 deploy application을하지만 다른 설정파일을 가지고한다.    
만약 둘다 deployment를하면 서로를 override한다. 젠킨스같이 자동화툴을사용하면 모른다 지네도 override됫는지  

3. 리소스공유 
4. 여러가지 버전을 한 클러스터에 넣고 싶을때
-현재버전 -다음버전 (blue and green deployment)
5. 네임스페이스를 2개만들어서 각각의팀이 관리를하는데 서로ㅓ의 네임스페이스에 접근못하게 막아버림으로써 우연히 다름 팀 네임스페이스를 건드려서 망가트릴일을 업생ㅁ
ns마다 컴퓨터자원을 제한걸수있따.

***
### 네임스페이스 이용시 고려해야할 사항
1. You cannot access most resources from another namespace  
예를들어 프로젝트1 프로젝트2가 같은 db를사용해서 configmap이 같아도 되지만 (secret도 마찬가지)  
프로젝트1에서 만든 configmap을 프로젝트2 네임스페이스에서 참고할수없고 프로젝트2에서 따로 만들어줘야한다.

2. service는 네임스페이스끼리 공유가능  
 즉 프로젝트 1과 프로젝트2내부의 configmap에서 database에 있는 mysql-service를 참고가능하다.


### 네임스페이스 내부에 생성될 수 없는 컴포넌트
- VOLUME,-> 네임스페이스 내부에 없으므로 클러스터 전체에서 접근가능  
kubectl api-resources --namespaced=false 로 이러한 컴포넌트들 검색가능
- NODE

### 네임스페이스 내부에 컴포넌트 생성방법
1. kubectl apply -f mysql-configmap.yaml --namespace=my-namespace
2. 두번째 방법 :configuration 파일 이방법이 좋음(AUTO INTEGREATION시 유용)
![image](https://user-images.githubusercontent.com/22045187/107509279-a7184300-6be5-11eb-8d45-4dd89e5d8b6a.png)


