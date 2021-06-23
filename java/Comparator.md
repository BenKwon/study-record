
comparator // comparable  
Arrays.sort  
vs  
Collections.sort  


```java
Arrays.sort(lectures, new Comparator<int[]>() {
            @Override
            public int compare(int[] t1, int[] t2) {
                if(t1[0] > t2[0]){ //오름차순
                    return 1;
                }
                else if(t1[0] == t2[0]){
                    if(t1[1] < t2[1]){ //내림차순
                        return 1;
                    }
                }
                return -1;
            }
        });

```
