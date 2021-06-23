
comparator // comparable  
Arrays.sort  
vs  
Collections.sort  

## 2차원 배열에서의 정렬
> 2차원 배열에서 각 원소의 1번째 혹은 2번째로 원소중에 특정 원소로 정렬
> 아래 코드는 sample[N][2]를 정렬하는 코드이다.  
> 
```java
        Arrays.sort(lectures, new Comparator<int[]>() {
            @Override
            public int compare(int[] t1, int[] t2) {
                if(t1[0] == t2[0]){ //0번째 원소가 
                    return t1[1] - t2[1]; //1번째 원소로 오름차순 정렬
                }else{
                    return t1[0] - t2[0]; //0번째 원소로 내림차순 정렬
                }
            }
        });
```
