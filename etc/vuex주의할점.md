# vuex 주의할점 


### 1. computed, method 사용의 중요성
![image](https://user-images.githubusercontent.com/22045187/136646276-bbd4aae9-b464-4e99-80ee-326a26302f49.png)

현장실습 프로젝트 중에
위와 같이 부모 태그에서 v-for로 생긴 index값을 자식 태그 v-for에서 사용하고 싶었다. 처음에 안되어서 index값 자체를 태그의 옵션 값들에 적용이  
안되는건가 싶었는데..
자식태그에서는 this가 부모를 가르키기 때문에 this가 내가 원하던 this가 아니었던 것이다.
따라서 computed나 method를 이용하여 원하는 값을 가져와야 한다.

이 이슈는 간단해보이는데 vue는 깊게 공부하지 않아 1시간 정도 서칭하다 결국 스택오버플로우에 질문을 하였다.  
[[자세한 답변](https://stackoverflow.com/questions/69489932/how-to-use-index-variable-generated-from-v-for-from-prent-tag-in-child-tags-v-f)]
