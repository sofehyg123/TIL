**예제로 배우는 스프링 입문-백기선님 강의 내용 정리**
====================================

## 목차
---
1. [프로젝트 설정](#프로젝트-설정)  
2. [프로젝트 살펴보기](#프로젝트-살펴보기)
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
  
  
* [목차로 돌아가기](#목차)  
  
***
  
  
## 프로젝트 살펴보기  
  
1. Debug 로그레벨 주석 풀기.    
>src > main > resources > application.properties > #loging 부분 > Debug 주석문 풀기.  
프로그램 실행 시 새로고침 등 요청에 찍히는 로그를 확인하기 위함임.  
INFO 레벨은 로그인, 상태변경과 같은 정보성 메세지를 나타냄.  
  
  
2. DispatcherServlet(GET/POST)  
**GET**  요청에 따른 해당 URL을 DistpatcherServlet 이 맵핑된 메서드와 연결해줌.  
**POST** 입력된 데이터를 파라미터 값으로 서버에 전달.(응답정보,POST방식).
```
POST>
2020-06-13 16:28:15.905 DEBUG 1046 --- [nio-8080-exec-7] o.s.web.servlet.DispatcherServlet: 
                                                     POST "/owners/new" parameters={masked}s
```
  
3. resources > templates > owners > ...html  
html 파일들은 resources 디렉토리 하에 templates 하에 위치해 있다는 것을 기억했다.  
html이 스프링 부트에서만 이렇게 위치하는 건지 아무데나 위치해도 상관이 없는지 모름. 찾아보기. 
  
  
4. 디버그 실행  
해당 line 왼쪽에 점을 찍으면 오류, 흐름을 찾는데 유용.  
다음단계는 F8,다음 커서는 F9  
  
  
***  
  
## * 내가 한 번 풀어보기 과제  
+ LastName 이 아니라 FirstName으로 검색 해볼까?  
    - 이번 장에서 로그 읽는 법을 배웠기 때문에 역 추적해서 해당 메서드 소스보기로 이름만 바꿔서 문제를 해결함.  
    - 자바 기본 개념을 숙지했기 때문에 스프링 문법(?) 용어에 대해서 하나도 몰랐지만 구조를 변경하는 것이 아니기 때문에 크게 어렵지 않았다.  
    - 코드 울렁증을 가지고 있지만, 싼 똥을 뚫어져라 바라볼 수 있는 의지로 극복함.  
    - 6시간 걸린 것 같다. 뚫어져 봐도 무슨내용인지는 모르겠지만 어떤 구조인제 대충(10퍼센트) 알았다. 자 이제 두번째 문제 시작!  
+ 정확히 일치하 것이 아니라 해당 키워드가 들어 있느 걸 찾아볼까?  
    - 내가 희미하게 기억하고 있는 쿼리, LIKE 대충 %name% 이런모양으로 감싸주면 name을 포함하여 검색되는 걸로 알고 있는데, 될까?  
    - 오류났다. 확인해보니 내가 수정한 쿼리에 문법오류가 난 듯 하다.  
    - 구글검색 > 스택오버플로우 > CONCAT('%',:username,'%')")  
    - 해결했다.  
+ Owner 에 age 추가  
    - 뷰,컨트롤러,쿼리를 수정해야 함.  
    - 호출경로를 파악하기 위해서 생성,결과 뷰 호출 로그를 봐야 했음.  
    - 인적사항과 관련된 객체에도 새로운 age 변수 선언 및 getter/setter, append() 작성.  
    - 이것 저것 만져보다가 쿼리 문에서 막힘. INSERT문에 해당 age용 저장 size 1개를 더 추가해야 돌아갈 것 같은데..  
    - 못품 일단 백기선님 풀이과정 보기로 함.  
  
  
## * 백기선님 과제 풀이  
+ LastName 이 아니라 FirstName으로 검색 해볼까?  
    - (깨달음1) 메서드를 수정하지 말고 새로 만들면 되는구나, 복사 붙여넣기 해서.  
    - (꺠달음2) 어떤게 어떤 걸 참조하고 있는지 구조를 재대로 모르겠다. 기본이 필요함.  
  
  
+ 정확히 일치하는 것이 아니라 해당 키워드가 들어 있는 걸 찾아볼까? 
```
@Query("SELECT DISTINCT owner FROM Owner owner left join fetch owner.pets WHERE owner.firstName 
        LIKE %:firstName")
// :firstName => 콜론까지 포함해서 키워드로 바뀌는 것이므로 콜론 앞에다가 % 해야 함.
```
+ Owner 에 age 추가  
    - 내가 생각했던 거랑 거의 일치했다. 그러나 나는 생각만 했고 시도하지 못했다. 반성한다.  
    - application.properties 에서 사용하고 있는 db정보를 확인할 수 있다. db에는 h2를 사용하고 db에 age 칼럼 추가,  
      insert문에 age자리에 칼럼 추가 해주면 끝  
    > db는 resources 밑에 있음.  
  
* [목차로 돌아가기](#목차)
***
