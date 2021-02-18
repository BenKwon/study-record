# 젠킨스 파이프라인 웹훅
> 일반 job의 경우 기본적으로 있는 Github Webhook Trigger을 이용하면 되지만 젠킨스 파이프라인에서는 해당 트리거를 사용하려면 추가적인 작업이 필요해 보인다.
하지만 Generic Webhook Trigger Plugin을 사용하면 쉽게 파이프라인에서도 push 웹훅을 할 수 있다.

## 1. Generic Webhook Trigger Plugin을 설치한다.
>플러그인 관리에서 Generic Webhook Trigger Plugin을 검색하여 설치해준다.

## 2. 파이프라인 설정
>파이프라인 설정에 들어가면 해당 옵션이 새로 생겨있다. 체크 해준다.
![image](https://user-images.githubusercontent.com/22045187/108365160-3bb02000-723a-11eb-8ea9-a03a69a67376.png)

>체크를 하게되면 해당 플러그인 관련 설정이 쭉 나오는데 그 곳에서 token항목을 찾아 임의로 아무거나 적어준다.
![image](https://user-images.githubusercontent.com/22045187/108365408-86319c80-723a-11eb-9416-5653c63db0a8.png)

http://JENKINS_URL/generic-webhook-trigger/invoke?토큰값 주소로 접속하면 webhook trigger가 실행된다.  
쿼리 파라미터 말고 헤드에 < token: 헤드값 >을 추가해도 된다.
