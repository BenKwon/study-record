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
### 3.status
