**예제로 배우는 스프링 입문-백기선님 강의 내용 정리**
====================================

## 목차
---
1. [프로젝트 설정](#프로젝트-설정)  
2. [프로젝트 살펴보기](#프로젝트-살펴보기)  
3. [스프링 Ioc](#스프링-Ioc)  
4. [Ioc 컨테이너](#Ioc-컨테이너)  
5. [Spring Bean](#spring-bean)  
6. [의존성 주입](#dependency-Injection)
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
        LIKE %:firstName%")
// :firstName => 콜론까지 포함해서 키워드로 바뀌는 것이므로 콜론 앞에다가 % 해야 함.
```
+ Owner 에 age 추가  
    - 내가 생각했던 거랑 거의 일치했다. 그러나 나는 생각만 했고 시도하지 못했다. 반성한다.  
    - application.properties 에서 사용하고 있는 db정보를 확인할 수 있다. db에는 h2를 사용하고 db에 age 칼럼 추가,  
      insert문에 age자리에 칼럼 추가 해주면 끝  
    > db는 resources 밑에 있음.  
  
* [목차로 돌아가기](#목차)
***  
  
  
## 스프링 Ioc  
  
1. Inversion of control : 제어의 역전  
    + 직접 new 하여 객체르 생성해서 직접 관리하는 것이 일반적인 의존성.
    + 그러나, 외부에서 의존성을 받아 본 클래스에 주입시키는 것, 다시 말해 외부에서 제어권을 가지고 의존성을 넘겨 받는 이 자체를 제어의 역전이라 함.
    + 의존성을 넘겨받을 때 밑에 코드 처럼 주입 시키는 형태를 DI(Dependency Injection) 이라고 함.
```  
//===============IoC=================
class OwnerController {
    private final OwnerRepository owners;
                            //Dependency Injection.
    public OwnerController(OwnerRepository owners){
        this.owners = owners;
    }
//===============IoC=================
```  

    + spring-petclinic 예제  
```
class OwnerController {
	public OwnerController(OwnerRepository clinicService, VisitRepository visits) {
		this.owners = clinicService;
		this.visits = visits;
	}
}
```
+ 내생각임.  
+ 결론부터 말하자면 위 생성자는 Repository가 생성되서 의존성을 받도록 설계되어 있음.  
+ 생성자가 1개 이기 때문에 OwnerController 클래스를 사용하기 위해서는 결국, OwnerRepository에 의존성 객체를 주입시킬 수 밖에 없다.  
+ 생성자가 1개 이기 때문에? 이게 무슨 말이지? 자바의 정석에서 배웠다.  
+ 클래스에서 생성자를 만들지 않는 경우에는 컴파일러에서 컴파일 과정 중 기본 생성자를 만들어 준다. 
+ 그렇지만 사용자가 인자가 있는 생성자를 만들 때, 컴파일러에서는 자동적으로 기본 생성자를 만들어 주지 않는다.  
+ 외부에서 클래스를 호출 할 때 new 연산자를 사용하여 생성자를 호출한다. 결국 그 객체를 사용하기 위해서 repository 객체 주입?  
+ 분명 이 클래스 자체에서는 repository 값이 null 일 듯. 하지만 외부에서 주입받을 수 있도록 설계 되어 있기 때문에 exception은 발생하지 않을 것이다.  
+ 그걸 편리하게 설계할 수 있는 기능을 spring이 제공해준다? 

    
* [목차로 돌아가기](#목차)  
***  

## Ioc 컨테이너
* :(ApplicationContext에 대해서 자바 api 문서 확인 > 구조 대강 파악하기)

* 스프링이 제공하는 Ioc 컨테이너 코드 : ApplicationContext(BeanFactory)  
* 핵심적인 클래스이지만 소스코드에서 직접 사용할 일이 없는 코드이기도 함.
* Ioc 컨테이너: 사실상 BeanFactory, ApplicationFactory는 BeanFactory를 상속 받고 있고 그 기능을 사용 할 수 있으며,  
	      더 많은 기능을 가지고 있다.
* Ioc 컨테이너의 구체적인 기능  
	1. Bean 을 생성. 그리고 Bean들 사이에 의존성을 엮어주고(관계), 이 빈들을 제공해주는 역할.  
* 그러나 모든 클래스(객체)들이 빈으로 등록 되어 있는 것이 아님. spring에서 그것을 확인하는 방법은 왼쪽에 녹색콩이 보이는지 안보이는지로 알 수 있음.  
* 녹색콩: 빈으로 등록된 클래스, 없음: 빈으로 등록되지 않은 클래스.  
* 앞서 썼던 생성자의 인자인 repository를 Ioc 컨테이너의 ApplicationContext가 해당 타입에 맞는 Bean을 찾아서 DI 시킨다.  
* 주의할 점: Ioc 컨테이너 안에 있는 객체들(Bean들) 끼리만 의존성 주입이 가능함.  
  
* 직접 Bean 등록하는 코드 살펴보기 : petclinic > system > CashConfiguration 에서 애노테이션 @Bean 코드르 확인할 수 있다.  
* @ResponseBody : return 하는 문자열 값이 응답정보의 본문이 되게끔 해주는 애노테이션  
* @GetMapping : 요청을 핸들러로 맵핑해주는 애노테이션  
  
* ApplicationContext 객체를 선언하면 의존성 주입을 받도록 설계되어 있기 때문에 생성자에 추가.

* 백기선님과 따라 쳐봤는데 코드에 빨간 밑줄... 일단은 intelliJ 커뮤니티 버전이라서 그런건가? 라고 생각하고, 좀 더 공부해서 다시 봐야지.


  
  
  
  
* [목차로 돌아가기](#목차)  
***  
## spring bean  
  
  
* bean
  - Ioc 컨테이너가 관리하는 객체  
```
OwnerController owner = new OwnerController(); -> bean이 아님
OwnerController bean = applicationContext.getBean(OwnerController.class); -> applicationContext에서 
가져다 쓰고 있기 때문에 bean이 맞음. 즉, applicationContext가 담고 있는 객체에만 의존성주입이 가능.
```
  
  - 빈을 등록하는 방법  
     1. Component Scannig  
     	+ @Component(메타 애노테이션)
	  - @Repository   
	  - @Service  
	  - @Comtroller  
	  - @Configuration
     2. xml, 자바설정 파일에 직접 등록  
  - 애노테이션 프로세스 중이 스프링 Ioc 컨테이너가 빈을 등록할 때 사용하는 인터페이스들을 라이프 사이클 콜백이라고 부름.  
  - 여러가지 라이프 사이클 콜백 중에는 이런 @Component 가 붙어있는 클래스를 찾아서 인터페이스들을 만들어 Bean으로 등록하는,  
  - 애노테이션 프로세서(애노테이션 처리기)가 등록되어 있음.  
  - @Component Scanning(애노테이션 프로세서?처리기?) 이 위치한 파일부터 하위 모든 디렉터리의 @Component 달린 클래스를 찾아서 Bean으로 등록하는 역할. 
  - 따라서 만든 클래스에 @Component 애노테이션을 붙여주면 직접 Bean을 등록하지 않아도 스프링이 Ioc컨테이너가 만들어질 때 알아서 Bean으로 등록해줌.  
  - Repository는 스프링 데이터 JPA가 제공해주는 기능에 의해서 Bean으로 등록.  
  - 이 경우는특별한 애노테이션이 없더라도 이 인터페이스를 상속 받는 클래스를 찾아서 클래스 안에 인터페이스 구현체를 내부적으로 만들어서 Bean으로 등록되는 과정임.  
  - 백기선님 강좌의 스프링데이터JPA 강좌에 설명하고 있다고 함. 들어야지.
    
    
```
@Configuration
public class SampleConfig{

	@Bean
	public SampleController sampleController(){
		return new SampleController();
	} //Bean 을 직접 정의
	  //SampleController() 객체를 리턴하는 메서드 자체가 Bean으로 등록이 됨.
	  //그러면 SampleController 클래스 위에 @Controller 애노테이션을 붙일 필요가 없게 되는 거임.
	  //위의 Bean 설정 파일을 사용했기 때문에...
	  //Configuration도 Component 이기 때문에 Component Scan이 됨.
	
```
  - @Autowired 애노테이션을 사용하면 Ioc 컨테이너에 있는 Bean을 꺼내서 사용할 수 있음.
  


* [목차로 돌아가기](#목차)  
***  

## dependency-Injection
  
* @Autowired 애노테이션 사용 후 메이븐 패키지 빌드 하면 Build False 되면서 실행이 중지된다.  
* (default) on project spring-petclinic: Formatting violations found in the following files  
* 스프링 문법 규칙에 맞는 문법으로 수정하라는 오류메세지인데, 빈 객체에서는 생성자를 통해 빈 객체를 주입받는 문법만 허용해서 발생하는 오류라고 한다.  
* 해결방법은 1.,/mvnw spring-javaformat:apply 해서 설정한 후 다시 ./mvnw package 빌드 실행하면 성공. 아니면,  
* git stash > git log - : 이게 무슨 의미인지 찾아봐야 겠다.  
* git stash : 하던 작업 임시로 저장하기.  
* git log - : 저장소의 commit history를 시간 순으로 보여준다.  
* 스프링 4.3 이후부터는 클래스에 생성자가 한개이고 그 생성자로 주입받는 reference 변수들이 Bean으로 등록되어 있다면 그 Bean을 자동으로 등록.  
* 할 수 있도록 스프링 프레임 워크에 추가됨, 그래서 생성자에 붙는 Autowired 애노테이션을 생략할 수 있음.   
   
* (티끌지식)테스트가 잘 동작한다는 의미는 빌드가 잘 동작이 된다는 거고 이 말은 이 코드가 재대로 동작한다는 의미.  
* setter 에 @Autowired 붙여도 됨. 이 방법 역시 Ioc컨테이너 안에 해당 타입의 Bean을 찾아서 넣어주는 것임.  

* (정리)Di 주입 방법 세 가지
	1. 필드 = OwnerController 의 @Autowired.
	2. 생성자 = OwnerController(OwnerRepository repository ... ) 의 @Autowired.
	3. setter = public void setOwner(){} 의 @Autowired.  
* 이 중에서 스프링 레퍼런스에서 권장하는 방법은 생성자.  
* 그 이유는 필수적으로 사용해야 하는 레퍼런스 없이는 이 인스턴스를 만들지 못하도록 강제할 수 있기 때문에 생성자가 좋은 선택일 수 있음.  
* 1번 2번은 의존성 없이도 클래스를 만들 수 있음.  
* 근데 1번 2번 같은 경우 순환참조시에 장점이 될 수 있음. 셍성자 같은 경우 못만듬. 순한참조 A가 B를 참조 B가 A를 참조.  
* 셀프 과제 : 의존성 주입시 해당 변수에 final을 붙이는 이유를 알아보자. final 개념 찾기(자바의정석), 그리고 영상 다시 시청.
* setter을 쓸 때에는 변수에 final을 쓰면 안됨. final의 개념을 다시 되새기기.



