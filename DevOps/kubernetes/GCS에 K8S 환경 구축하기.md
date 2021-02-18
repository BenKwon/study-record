# GCS에 K8S 환경 구축하기
>사실 GKE(google kubernetes engine)를 쓰면 되지만 이전에 virtual box에서 쿠버네티스를 다뤄본 경험이 있어 Compute Engine으로 구축을 하는것이 더 편하다고 생각되고 젠킨스랑 연동할때도 추가적인 웹 서칭없이 스스로 바로 가능할 것 같아서 Compute Engine에 마스터노드와 워커 노드를 만들기로 하였다.

## 노드 최소 사양
- 마스터 노드 : CPU 2코어, 램 2기가
- 워커 노드 : CPU 1코어, 램 1기가
> 가상 머신에서 K8S를 가지고 놀때 사실 내 노트북만이 아니라 내 데스크탑에서도 연동해서 사용하고 싶어서 아마존 EC2를 활용해서 구축하려 했는데  
무료 티어 t1.micro에서는 사양이 안되어서 구축을 실패 하였다.  
그래서 이번에는 GCS에서 주는 무료 크레딧을 이용하여 최소 사양을 맞추어 진행하였다.
크레딧을 주긴하지만 사용을 최소화 하기위하여 마스터 노드1개 워커 노드1개로 환경을 구축하겠다.


**내가 사용한 노드 사양**  
- 마스터 노드(master) : e2-medium(vCPU 2개, 4GB) 메모리, Ubuntu 18.04 LTS
- 워커 노드(worker1) : e2-medium(vCPU 2개, 4GB) 메모리, Ubuntu 18.04 LTS  


## - 마스터 노드 & 워커 노드 공통 
1. 각 노드에 hostname을 설정해준다.

```cmd
$ sudo hostnamectl set-hostname "master"
$ sudo hostnamectl set-hostname "node1"
```

2. apt 패키지 리스트 업데이트 

```cmd
$ sudo apt-get update
```
3. apt 패키지 리스트 업데이트 

```cmd
$ sudo apt-get update
```

4. Docker gpg key를 추가

```cmd
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

5. Docker의 레포지터리를 추가

```cmd
$ sudo add-apt-repository    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
  stable"
$ sudo apt-get update
```

6. k8s gpg key를 추가

```cmd
$ curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

7. k8s의 레포지터리를 추가

```cmd
$ cat << EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
$ sudo apt-get update
```

8. Docker, kubelet, kubeadm, kubectl 설치
```cmd
$ sudo apt-get install -y docker-ce=18.06.1~ce~3-0~ubuntu kubelet=1.15.7-00 kubeadm=1.15.7-00 kubectl=1.15.7-00
```
9. kubelet, kubeadm, kubectl의 버전 holding 설정
```cmd
$ sudo apt-mark hold docker-ce kubelet kubeadm kubectl
```
10. Swap disabled
```cmd
$ swapoff -a && sed -i '/swap/s/^/#/' /etc/fstab
```
11. sysctl.conf에 iptables rule 추가
```cmd
$ echo "net.bridge.bridge-nf-call-iptables=1" | sudo tee -a /etc/sysctl.conf
$ sudo sysctl -p
```
```cmd
$ cat <<EOF > /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
$ sysctl --system
```

## - 마스터 노드 세팅
1. 쿠버네티스 클러스터를 초기화

```cmd
$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

2. Set up Local Kubeconfig

```cmd
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
마스터 노드를 재부팅하면 위 명령어를 다시 쳐줘야 kubectl 명령어가 작동한다. 위 명령어는 쿠버네티스 문서에서 가져왔다.

3. Flannel CNI network overlay 적용

```cmd
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## - 워커 노드 세팅
아래 명령어는 마스터 노드에서 init을 하면 출력되는 명령어이다. 이 명령어를 워커 노드에서 실행하면 마스터 노드에 등록 된다. 
```cmd
kubeadm join 10.128.0.8:6443 --token 04v6u8.abm0eq0t3dbet0m2 \
>     --discovery-token-ca-cert-hash sha256:c80ce023e3c9161bc9d0cb065bdaf4297bcadc370a3868d261471ec2cd9be1a4
```




### 참고
가끔 오류가 나거나 재부팅시 kubeadm init을 해서 쿠버네티스를 다시 초기화 시켜주는데 이때 오류가 나면 보통 하단의 명령어로 해결된다.  
```cmd
kubeadm reset
```
 
```cmd
export KUBECONFIG=/etc/kubernetes/admin.conf
```

