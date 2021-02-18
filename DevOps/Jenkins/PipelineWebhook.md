# 젠킨스 파이프라인 git 웹훅
> 일반 job의 경우 기본적으로 있는 Github Webhook Trigger을 이용하면 되지만 젠킨스 파이프라인에서는 해당 트리거를 사용하려면 추가적인 작업이 필요해 보인다.
하지만 Generic Webhook Trigger Plugin을 사용하면 쉽게 파이프라인에서도 push 웹훅을 할 수 있다.

### 1. Generic Webhook Trigger Plugin을 설치한다.
>플러그인 관리에서 Generic Webhook Trigger Plugin을 검색하여 설치해준다.

### 2. 파이프라인 설정
>파이프라인 설정에 들어가면 해당 옵션이 새로 생겨있다. 체크 해준다.
![image](https://user-images.githubusercontent.com/22045187/108365160-3bb02000-723a-11eb-8ea9-a03a69a67376.png)

>체크를 하게되면 해당 플러그인 관련 설정이 쭉 나오는데 그 곳에서 token항목을 찾아 임의로 아무거나 적어준다.
![image](https://user-images.githubusercontent.com/22045187/108365408-86319c80-723a-11eb-9416-5653c63db0a8.png)

http://JENKINS_URL/generic-webhook-trigger/invoke?토큰값 주소로 접속하면 webhook trigger가 실행된다.  
해당 주소를 깃 레포지터리 설정의 webhook카테고리에서 추가하면 된다.  
쿼리 파라미터 말고 헤드에 < token: 헤드값 >을 추가해도 되지만 깃 연동을 위해서는 Query Parameter을 이용해야 한다.  


# 젠킨스 파이프라인 웹훅 bracnh 구별
> master 브랜치로 push될때만 웹훅 트리거가 발생하도록 설정하려고 한다. 일단 깃허브에서 보내는 webhook trigger에서  
젠킨스에서 parsing을 할 수 있또록 content type을 json으로 지정한다.
![image](https://user-images.githubusercontent.com/22045187/108367077-68fdcd80-723c-11eb-9f01-13f397e6d4a9.png)

>젠킨스 대쉬보드로 돌아와서 이전에 Generic Webhook Trigger Plugin을 체크하면 뜨던 설정목록에서 Post content Parameters를 찾는다.  
해당 파트에서 Variable에 변수명을 적어주고 Expression에는 $.ref를 적어준다. ref는 브랜치명이다. (웹훅으로 Post로 전송되는 head값 변수명)  

>다음으로는 Optional Filter파트로 이동해서 Expression에는 정규 표현식으로 ^(refs/heads/main)$ 을 입력해주고 Text에는 바로 직전 과정에서
Variable에 입력했던 변수명을 적어준다. ex) $변수명  
![image](https://user-images.githubusercontent.com/22045187/108367723-140e8700-723d-11eb-8c30-9dc64d70c088.png)


깃에서 POST방식으로 refs/heads/브랜치명 을 포스트 방식으로 "ref"변수에 넣어서 전송한다. 그럼 젠킨스에서는 해당 값을 가져와  Expression에 해당하는 정규 표현식과 맞아   떨어지는 경우에만 파이프라인이 작동된다.
