# 자바 String, StringBuilder 그리고 StringBuffer 성능 차이 비교

```java
System.out.println("출력값")
```
>가장 느림

 
```java
BufferedWriter.write("출력값")

BufferedWirter.flush()
```
>빠름

 
```java
StringBuilder.append("출력값")

System.out.println("출력값")
```
>조금 더 빠름
