**예제로 배우는 스프링 입문-백기선님 강의 내용 정리**
====================================

## 
---
[프로젝트 설정](#프로젝트-설정)    

***
    
    
    

## 프로젝트 설정    
실행 방법은 해당 프로젝트 설명란(Running petclinic locally)애서 확인하 수 있음.    

```
git clone https://github.com/spring-projects/spring-petclinic.git  //1
cd spring-petclinic                                                
./mvnw package                                                     //2
java -jar target/*.jar                                             //3
```
* 1  
인텔리J라는 편집도구를 통해 git clone 받음.  
(IntelliJ 초기화면 > Get from Version Control > Version control : Git으로 설정 > 해당 URL clone)  
  
**해당 경로에 이미 같은 프로젝트 이름을 가진 폴더가 존재한다면 삭제,**  
>터미널창 : (해당 프로젝트폴더가 있는 경로)rm -rf 프로젝트 이름  
  
  
* 2  
 ./mvnw package : 로컬에 메이븐을 설치하지 않았더라도 사용할 수 있도록 함.  
                  maven에 패키징 빌드를 실행하면, 이 프로젝트를 빌드해서 패키징 파일을 만듬.
>(갑툭튀)강의에서 백기선님이 오타내서 오류가 났는데, 이 떄 오류메세지를 항상 읽는 습관.
> jar 패키지 확인 방법: target > pom.xml 에 packaging 태그가 없으면 기본적으로 jar파일임.
>./mvnw package 실행 해보면,  
>Building jar: /Users/yunhyeoggi/workspace/spring-petclinic/target/spring-petclinic-2.3.0.BUILD-SNAPSHOT.jar
으로 확인 할 수 있다.

  
* 3  
java -jar : jar로 만들어진 파일을 실행하는 명령어  
실행하면 톰캣이 8080 포트에 떳다는 것을 로그 메세지엣 확인하 수 있음.  
localhost:8080 접속하면 해당 프로젝트 페이지가 보임.
  
  
* 애플리케이션 종료 : (터미널에서)ctrl + c  
  
* 애플리케이션을 실행하는 또 다른 방법  
java  > PetClinicApplication 의 main 메소드 실행  
주의할점*  
: 자바의 메인 메소드를 하기 전에 메이븐 패키징을 꼭 해야 함.  
 메이븐 패키징하는 과정 중에 프론트 관련 라이브러리를 생성해 내는 플러그인이 있는데 그 플러그인이 동작해야만 로컬호스트에서 꺠지지 않고 온전하게 동작할 수 있음.  
따라서 한 번이라도 메이븐 페키징을 하고 난 뒤로부터는 자바 애플리케이션을 실행/종료 해도 됨.  
  
* 끝마치며)  
스프링 펫클리닉이라는 스프링 예제 프로젝트 깃 클론을 메이븐으로 빌드하고 인텔리 제이로 읽어들여서 메이븐을 사용해서 빌드를 하고 애플리케이션을 실행  
  
* 애플리케이션 두 가지 방법.  
1.콘솔창에 java -jar을 사용하는 방법  
2.인텔리J에서 펫 클리닉 에플리케이션이라는 메인 메소드를 실행해서 애플리케이션을 실행하는 방법.  
  
애플리케이션 종료시 찍히는 로그들.  
```
Closing JPA EntityManagerFactory for persistence unit 'default'
 Shutting down ExecutorService 'applicationTaskExecutor'
HikariPool-1 - Shutdown initiated...
 HikariPool-1 - Shutdown completed.
 Cache 'vets' removed from EhcacheManager.
```
* 서버포트 변경  
java --server.port=하고싶은 포트번호  
[목차로 돌아가기](#목차)  
***

