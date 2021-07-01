# BufferedReader,Writer
>알고리즘을 푸는데 문자열 출력 때문에 시간초과가 나서 StringBuilder 를 사용하게 되었다.  
>나중에 버퍼를 안쓰고 Scanner쓰다가 시간초과가 날까봐 앞으로는 버퍼를 사용해서 입력을 받고 StringBuilder로 출력하려고한다.
>참고로 scanner가 느린이유는 regex를 쓰기 때문이라고한다.

```java
BufferedReader bf = new BufferedReader(new InputStreamReader(System.in)); 
String s = bf.readLine(); //String
int i = Integer.parseInt(bf.readLine()); //Int
```
주의 할 점은 예외처리를 꼭 해야한다. 메인 함수에 throws IOException을 추가해줘야한다.  

### Read한 데이터 가공
```java
StringTokenizer st = new StringTokenizer(s); //StringTokenizer인자값에 입력 문자열 넣음
int a = Integer.parseInt(st.nextToken()); //첫번째 호출
int b = Integer.parseInt(st.nextToken()); //두번째 호출
```

## Reader 종합 코드

```java
BufferedReader bf = new BufferedReader(new InputStreamReader(System.in)); 
int i = Integer.parseInt(bf.readLine()); //Int

String s = bf.readLine(); //String
StringTokenizer st = new StringTokenizer(s); //StringTokenizer인자값에 입력 문자열 넣음
int a = Integer.parseInt(st.nextToken()); //첫번째 호출
int b = Integer.parseInt(st.nextToken()); //두번째 호출
```

