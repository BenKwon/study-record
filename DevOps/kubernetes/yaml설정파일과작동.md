# K8S YAML CONFIGURATION FILE
>쿠버네티스는 YAML 파일을 활용해 Configuration을 작성한다.   
yaml파일을 활용하여 쿠버네티스의 Components를 생성, 삭제, 업데이트를 할 수 있다.
## yaml 파일 예시

```yaml
# nginx-deploy.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```

### 명령어 예시

```bat
kubectl apply -f nginx-deploy.yaml  // 생성 및 업데이트 
kubectl delete -f nginx-deploy.yaml // 삭제
```
>업데이트의 경우 생성 명령어와 같다. 이미 클러스터에 동일한 metadata.name을 가진 deployment가 존재한다면  
이를 쿠버네티스가 인지하고 UPDATE로 처리한다.

## Configurations file의 3가지 파트
### 1.metadata
일반적으로 생성하려는 컴퍼넌트 자체에 대한 정보로 컴퍼넌트를 특정할 name과 lables가 들어간다.

```yaml
# nginx-deploy.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
...
```
### 2.specification
그 yaml파일에서 생성하거나 수정할 컴퍼넌트에 대한 구체화이다. selector.matchLabels가 들어가거나 팟을 총 몇개 운용할건지에 대한 replicas  
그리고 configuration file내의 configuration 파트인 template부분이 들어간다. deployment yaml configuration파일에서는 template부분에 pod에대한
설계도(BluePrint)가 들어간다.
```yaml
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:  # 이 부분은 pod에 대한 configuration 정보이다. 따라서 pod의 metadata, spec부분이 따로 적혀있다.
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```
### 3.status
3번째로는 status파트인데 이부분은 따로 configuration file에 적지 않아도 쿠버네티스가 자동으로 만들고 추가한다.  
쿠버네티스는 이 status를 지속적으로 업데이트 해준다. (이 status는 etcd에서 얻어돈다)
만약 updated된 yaml파일을 적용할시 etcd에서 status를 가져와서 대조하고 다른부분은 추가해준다.

## Label Selector
>lables는 주로 metadata:labels  붙거나 deployment 설정파일의 경우 spec:selector:matchLables:app:, Service 설정파일의 경우   
spec:selector:app:에 붙는다. metadata에 붙는 경우는 자기 자신 (컴퍼넌트)를 라벨링 해주는 것이고 spec에서 selctor하위에 붙는 경우   
이 컴퍼넌트에서 해당 라벨을 기준으로 다른 컴퍼넌트에 연동될수 있도록 해주기 위함이다.
![image](https://user-images.githubusercontent.com/22045187/107497911-7977cd80-6bd6-11eb-8d9d-2b183b1c0a83.png)

서비스의 selecotor는 deployment와 pods을 찾아 연결하고 deployment의 selector는 pods을 찾아 연결한다.


## Service와 deployment의 configuration파일 비교

![image](https://user-images.githubusercontent.com/22045187/107498263-e723f980-6bd6-11eb-953f-e4e0c939aef5.png)

Service에서는 targetPort는 타겟 pod의 포트이다. 해당 서비스와 위 적힌 80포트를 이용하면 해당 팟으로 포워딩 된다.  
ex) DB Servcie에서 nginx Service로 포트 80 으로 연결하면 nginx service에서 포트 8080으로 pod으로 연결된다.   
DB SERVICE -> NGINX SERVICE -> POD

## Service와 deployment configuration 같이 쓰기
> 일반적으로 함께 사용되는 컴퍼넌트들은 한 yaml파일에 정의하기도 한다.
#### mongodb와 이를 위한 서비스를 한 yaml파일에 
```yaml
# mongo.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongodb-deployment
  labels:
    app: mongodb
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongodb
  template:
    metadata:
      labels:
        app: mongodb
    spec:
      containers:
      - name: mongodb
        image: mongo
        ports:
        - containerPort: 27017
        env:
        - name: MONGO_INITDB_ROOT_USERNAME
          valueFrom:
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-username
        - name: MONGO_INITDB_ROOT_PASSWORD
          valueFrom: 
            secretKeyRef:
              name: mongodb-secret
              key: mongo-root-password
---
apiVersion: v1
kind: Service
metadata:
  name: mongodb-service
spec:
  selector:
    app: mongodb
  ports:
    - protocol: TCP
      port: 27017
      targetPort: 27017

```
***
**기타 명령어**  
kubectl get pods -o wide를 이용하여 더 많은 정보를 볼수가 있다.  
kubectl get deployment nginx-deployment -o yaml // deployment를 yaml파일 형식으로 볼 수 있음.  
해당 yaml파일 내용을 다른 파일에다 써서 일부 내용을 지우면 우리가 다시 apply할 수 있는 yaml파일로 사용 가능하다.
***
