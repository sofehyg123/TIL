# Spring_boot

* [출처:백기선님의 inflearn 강좌](https://www.inflearn.com/course/스프링부트/dashboard)  
---
* 꺠닫기
  + 환경설정이 오타가 나거나 잘못된 데이터를 맵핑하는 코드를 넣으면 맵핑값을 찾을 수 없게 되어서 오류가 뜬다.  
# 목차  
    
* [자동설정 이해](#자동설정-이해)  
* [자동설정 만들기](#자동설정-만들기)  
* [내장 웹서버의 이해](#내장-웹서버의-이해)  
* [내장 웹서버의 응용1](#내장-웹서버의-응용1)  
* [내장 웹서버의 응용2](#내장-웹서버의-응용2)  
* [독립적으로 실행 가능한 JAR 파일](#독립적으로-실행-가능한-JAR-파일)  
* [spring application](#spring-application)  
* [외부설정](#외부설정)  
* [프로파일](#프로파일)  
* [테스트](#테스트)  
* [스프링 웹 MVC](#스프링-웹-MVC)  
    + [소개](#소개)  
    + [HttpMessageConverters](#HttpMessageConverters)  
***  
### 모르는 거 알기  
  
>#### @RestController  
* @Controller는 View를 리턴하는 메서드를 포함하지만, @RestController는 문자열(JSON 등)과 객체를 리턴하는 메서드들이 있다.  
* @RestController로 문자열 전송하기  
```  
@RestController
@RequestMapping("/test/*")
public class SampleController {
  @RequestMapping("/hello")
  public String hello(){
      return "hello"; //문자열 전송
  }
}
```  
* @RestController로 [커맨드](#커맨드) 객체 전송하기  
```  
@RestController
@RequestMapping("/test/*")
public class SampleController {

  @Autowired
  public UserVo userVo;

  @RequestMapping("/hello")
  public UserVo userVo(){
      userVo.setAddress("아아아아");
      userVo.setAge(25);
      userVo.setName("아아아아");
      return userVo;
  }
}
```  
* @RestController로 컬렉션 객체 전송하기  
* @RestController로 map 전송하기  
  
  
[참고](https://webcoding.tistory.com/entry/Spring-REST-API-사용하기)  

>#### GetMapping vs RequestMapping  
[출처](https://0penster.tistory.com/24)  
* spring 4.3 이후부터 @RequestMapping(value="/hello", method={RequestMethod.GET} 를 간단하게 @GetMapping로 사용할 수 있게 간편해졌다.  


***  
  
# 자동설정 이해  
  + 출처  
    1. 백기선님 스프링부트 강좌
    2. [AutoConfig에 대한 이해](https://blog.naver.com/PostView.nhn?blogId=sharplee7&logNo=221591429432&categoryNo=0&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)  
  + 빈은 두 단계에 걸쳐 읽힘.    
    1. ComponentScan  
        - @ComponentScan 혹은 @SpringBootApplication 애노테이션이 붙은 클래스가 위치한 패키지부터 하위패키지의 클래스들을 Scan한다.  
        - Scan은 아래 애노테이션들이 붙은 클래스를 Spring Bean으로 등록한다.  
        - @Component @Configuration @Repository @Service @Controller @RestController.  
        - 1차적으로 진행하는 스캔임.  
    2. EnableAutoConfiguration  
        - 2차적으로 진행되는 스캔인데 빈으로 등록한다는 개념은 비슷하지만 동작방식이 조금 다름.  
        - Maven의 의존성 관계에 있는 jar 파일을 기반으로 Configuration을 자동설정함.  
        - @EnableAutoConfiguration이 자동으로 등록하려는 Bean들은 External Library 의 spring-boot-auticonfigure:2.x.x.RELEASE > META-INF > spinrg.factories 안에 정의되어 있으므로  이 곳에다 configuration을 정의하면 Bean을 등록해줄 수 있게 @EnableAutoConfiguration이 스캔할 것임.  
        - 근데 factories에 EnableAutoConfiguration 키값을 적어야 동작할 듯?
        - 그래서 EnableAutoConfiguration이 동작할 때 이 키값에 해당되는 configuration을 구동시키는 거지.
        - 근데 전부 다 구동되는 것은 아니고 조건에 맞게 구동이 된다는 것. =conditional  
  
# 자동설정 만들어 보기
  
  1. pom.xml  
  - <dependencies> 태그 안에 autoconfigure이라는  의존성을 작성하고  
  - <dependencyManagement> 를 작성해서 의존성의 버전을 관리해주는 영역을 추가해줬다.  
  
  ```
  //pom.xml에서..
      <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-autoconfigure-processor</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.0.3.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
  ```
***  
> 기본개념 파악1([출처](https://maven.apache.org/guides/mini/guide-naming-conventions.html))  
  1.groupId   
  2.artifactId  
  3.version  
***  
  
  2. Scan할 패키지 위치에 클래스 생성(variable, getter/setter, toString())  
  
  3. @Configuration 애노테이션이 붙은 Configuration클래스 작성.    
     @Bean 등록할 메서드 및 객체를 주입할 클래스 인스턴스 생성 후 헤당 인스턴스의 setter 메서드르 호출해서 데이터를 주입 및 객체 리턴해줄 것.  
  
```  
  
  @Configuration
  public class HolomanConfiguration {

    @Bean
    public Holoman holoman(){
        Holoman holoman = new Holoman();
        holoman.setHowLong(5);
        holoman.setName("Hyeokki");
        return holoman;
    }
  }
```
  4. 해당 클래스의 자동 설정을 읽어 들일 수 있도록 resources 밑에 META-INF 디렉토리를 만든 후 spring.factories 파일에  EnableAutoConfiguration과  
  FQCN 작성.  
```
<spring.factories>

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\ //자동설정 key
    me.practice.HeymanConfiguration // FQCN (Full Quilified Class Name)
```
  5. maven install : 이 프로젝트를 빌드해서 jar 생성된 것을 다른 매이븐 프로젝트에서 쓸 수 있도로 로컬 메이븐 저장소에 설치.  
     변경이 일어나면 install을 해주고 의존성 받을 프로젝트에는 maven refresh를 해줘야 반영이 됨.  
  
  6. 다른 프로젝트에서 local maven 저장소에 저장된 Bean을 쓰기 위해서 pom.xml에서 자동설정을 작성한 프로젝트의 의존성을 작성해야함.  
     그럼 외부 라이브러리에서 자동으로 추가된다.  
```
<의존성을 받을 프로젝트의 pom.xml>
        <dependency>
            <groupId>me.practice</groupId>
            <artifactId>yun-spring-boot-starter</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```
  
  7. Bean이 있는지 확인 하기 위해서 Runner 클래스를 만들고 ApplicationRunner 을 구현해야 한다. 
  
```  
  @Component
  public class HolomanRunner implements ApplicationRunner {
            //ApplicationRunner는 스프링 부트를 만들고 실행했을 떄 자동으로 빈으로 만들고 싶을 때 쓰는 기능.

  @Autowired
  Holoman holoman;
  //Bean을 등록하지 않았지만 maven에서 의존성을 받아왔기 때문에 Ioc컨테이너에 있는 Bean을 Autowired로 꺼내서 쓸 수 있다.

  @Override
  public void run(ApplicationArguments args) throws Exception {
      System.out.println(holoman);
  }
```  
      
* 해당 프로젝트에서 AutoConfiguration에서 설정된 Bean과 같은 타입으로 직접 @Bean으로 등록한 경우 autoConfiguration에서 설정된 Bean이 우선시 된다.  
* @ComponentScan에서 읽는 @Bean이 우선시 되어야 하기 때문에 이런 현상을 해결해야 한다.  
* 그럴 때 @ConditionalOnMissingBean 애노테이션을 autoConfiguration으로 읽는 @Bean 밑에다가 추가하면 이런 현상을 해결할 수 있다.
* 이 의미는 해당 타입이 @Bean 등록이 되어 있지 않으면 이 @Bean을 등록하라는 뜻.
* @ComponentScan에서 직접 등록한 @Bean이 있기 때문에 다음 단계인 autoConfiguration 스캔할 때에는 해당 타입의 Bean울 등록하지 않게 되는 것.
* 이 때 maven maven 로컬저장소에 다시 install 해서 업데이트를 해줘야 수정된 결과가 maven에 반영된다.
  
***
  * 만약 일일이 직접 등록하지않고 내가 원하는 속성만 변경하고 싶을 때 resource > application.properties 에 해당 속성을 적어주면 됨.  
  
```   
  <resources/application.properties>
  holoman.name = yunhyeokki
  holoman.how-long = 10
```   
  * 이것이 적용되게 할려면 autoconfiguration 설정을 위한 프로젝트에서 
  ```
  1.
  <HoloProperties클래스>
  @ConfigurationProperties("holoman") : holoman으로 정하고 name과 howlong을 getter/setter한다.
  
  2.
  <HolomanConfiguration클래스>
  //이 등록된 properties를 사용할 수 있게 하기 위해서 @EnableConfigurationProperties(HoloProperties.class)을 등록한다.
  @EnableConfigurationProperties(HoloProperties.class)
  public class HolomanConfiguration {

    @Bean
    @ConditionalOnMissingBean
    public Holoman holoman(HoloProperties holoProperties){
        Holoman holoman = new Holoman();
        holoman.setHowLong(holoProperties.getHowLong());
        holoman.setName(holoProperties.getName());
        return holoman;
    }
  }  
    
  ```  
  
  * 그리고 maven install 해서 업데이트.  
  * propertis 자바 파일은 변경하는 안되는 클래스이기 때문에 신중하게 만들어야 함. 맵핑되는 클래스 이름이나 값이 바뀌면 properties값도 바꿔줘야 하는데 그것이 많으면 일일이 수정해야 하기 때문.  
  
  
    
    
* [목차로 돌아가기](#목차)  
***

# 내장 웹서버의 이해  

* 스프링 부트 자체는 일종의 툴임. 내장 서블릿 컨테이너를 쉽게 쓸 수 있도록.  
* ServletWebServerFactoryAutoConfiguration 서블릿 웹서버를 자동 설정으로 생성하는 configuration.  
    + 내장 톰캣을 autoconfiguration에서 만들어서 사용할 수 있음.  
        - 확인: spring.factories > ServletWebServerFactoryAutoConfiguration > embededTomcat 에서 확인.  
    + 이 클래스 안에는 TomcatServletWebServerFactoryCustomize 서버 커스터 마이징 기능을 하는 @Bean도 등록되어 있음.  
* DispatcherServletAutoConfiguration : DispatcherServlet을 생성하고 서블릿 컨테이너에 등록하는 일을 수행.  
  
***
# 내장 웹서버의 응용1

* 웹서버 동작하지 않기  
  + 코드로 작성하기  
  ```
  public class Application (){
      public static void main(String[] args){
          SpringApplication application = new SpringApplication(application.class)
          application.setWebapplicationType(WebApplicationType.NONE);
          application.run(args);
      }
  }
  ```
  + application.properties 에서 작성하기  
  spring.main.web-application-type=none  
  
* 웹 서버 포트 변경  
  + application.properties
    ```  
    server.port = 7070(주고 싶은 포트 번호)
    // server.port = 0 으로 해주면 랜덤 포트로 띄어서 찾아줌.(로그 기록에서 찾을 수 있음).
    ```  
  + 설정한 포트(랜덤 포트 포함)을 어디다 쓸 것인가?  
      -ApplicationListner<ServletWebServerInitializedEvent>  
    
```  
  @Component
    public class PortListener  implements ApplicationListener<ServletWebServerInitializedEvent> {
    @Override
    public void onApplicationEvent(ServletWebServerInitializedEvent servletWebServerInitializedEvent) {
               
        ServletWebServerApplicationContext applicationContext =         
                                      servletWebServerInitializedEvent.getApplicationContext();

        System.out.println(applicationContext.getWebServer().getPort());
        //서블릿 웹서버 context이기 떄문에 서버 정보를 알 수 있음. 그럼 포트 정보도 알 수 있겠지?  
    }
  }
```   
      - 웹서버가 생성되면 ServletWebServerInitializedEvent 통해 @Override된 메서드가 호출됨.  
      - 이 메서드 안에서 port 정보를 얻을 수 있고, 웹서버거 시작(=생성)되면 포트 정보를 확인 할 수 있다.  
```   
<log 기록>
Jetty started on port(s) 51300 (http/1.1) with context path '/' //1
51300                                                           //2
//1 =>웹서버가 시작(=생성)됨. 이 때 이벤트에 의해 호출된 메서드가 동작하고 거기에 getPort해서 
//    정보를 얻어올 수 있음을 바로 다음에 찍히는 로그를 보면 알 수 있다.
```   
  
***

# 내장 웹서버의 응용2  
  
  
* 내장웹서버에 HTTP1 과 HTTP2를 적용하는 방법을 알아보기.  
    + HTTP1(SSL) 적용
        - 사용하려면 keystore 을 만들어야 함.[참고](https://gist.github.com/keesun/f93f0b83d7232137283450e08a53c4fd)  
        - starting to set with keystore.
        - resources > application.properties 안에 밑 코드 작성.
          ```  
          server.sll.key-store=keystore.p12
          server.ssl,key-store-type=PKCS12
          server.ssl.key-store-password=123456
          server.ssl.key-alias=yun
          ```    
        - 스프링 부트에 톰캣이 사용하는 connector 가 하나만 등록되어 있는데 이 connector에 SSL적용해줌.  
        - 이제 앞으로 모든 요청은 https 로 요청  
        - 클라이언트가 요청하면 서버는 인증서를 보내는데 이 인증서의 key를 모르면 접근 거부됨.  
        - curl -I -http2 http://localhost:8080/hello ->400 error  
        - curl -I -http2 https://localhost:8080/hello  
        - 공인 인증서가 아니기 때문에 아래처럼 메세지가 뜨는데 -k 옵션값을 추가해주면 결과값을 확인할 수 있음.  
        >message
         If this HTTPS server uses a certificate signed by a CA represented in
         the bundle, the certificate verification probably failed due to a
         problem with the certificate (it might be expired, or the name might
         not match the domain name in the URL).
         If you'd like to turn off curl's verification of the certificate, use
         the -k (or --insecure) option.
         HTTPS-proxy has similar options --proxy-cacert and --proxy-insecure.  
         
         ```  
         - curl -I -k --http2 https://localhost:8080/hello  
         ```  
    + HTTP 커넥터, 코딩으로 설정하기  
      기존에 하나였던 connector을 SSL로 덮어서 쓰고 있기 때문에 새롭게 Connector 만들어서 http로 쓰기.  
      [참고출처](https://github.com/spring-projects/spring-boot/blob/v2.0.3.RELEASE/spring-boot-samples/spring-boot-sample-tomcat-multi-connectors/src/main/java/sample/tomcat/multiconnector/SampleTomcatTwoConnectorsApplication.java)    
      - 기존의 SSL 포트를 변경해서 http 포트를 8080 쓰게끔 하자.  
      - application.properties > server.port = 8080  
    + HTTP2 적용  
        - application.properties 에 server.http2.enabled=ture 을 추가해주면 되는데 웹 서버마다 제약사항이 다르니 공홈 확인.  
        - undertow 웹서버 사용하면 별다른 설정이 필요없음.  
        - 그리고 HTTP에 SSL이 기본적으로 적용되어 있어야 함.  
    + undertow 말고 tomcat서버로 HTTP/2 를 동작하기 위한 방법. pom.xml에 의존성 추가    
        - pom.xml의 properties 영역에 java와 tomcat 버전을 명시해서 변경해준다.
        - 자바9를 설치한다.
        - project structure에서 프로젝트 자바 버전을 9로 변경하고 module의 dependencies 에도 자바 9로 변경해준다.
        - 
    ```  
    <pom.xml>
       <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>9</java.version>
        <tomcat.version>9.0.36</tomcat.version>
    </properties>
    ```  
  
  
     
***

# 독립적으로 실행 가능한 JAR 파일  
  
* maven 프로젝트를 다른 곳에서 쓸 때 배포할 프로젝트를 jar파일로 packaging 해서 받는 곳에서 jar파일을 사용하는 것이 유용함.  
* mvn clean : target 밑에 있는 걸 전부 삭제 하는 기능  
* mvn package : jar 파일 생성하는데 이 안에 external libraries 이 전부 들어가 있기 때문에 의존성도 포함된다.  
* 문제 : 옛날에는 jar파일을 읽어들일 수 있는 자바의 표준적인 방법이 없었기 때문에 uber 라는 jar을 사용함.  
* 스프링부트에서는 의존성에 해당하는 jar파일들을 묶어서 jarFile이라는 로더를 통해서 읽어들일 수 있음.  
***
# spring application  
  
* log
    + DEBUG로 변경
      - Run/Debug Configurations 의 VM 에 -Ddebug 입력하고 apply  
      - 이 모드는 어떠한 자동설정이 적용 됬는지/안됬는지에 대한 문구를 확인할 수 있음  
    + Failure  
      - 오류메세지를 이쁘게 하는 방법  
    + banner  
      - resources > banner.txt > 원하는 내용  
      - 실행하면 내가 설정한대로 출력됨.  
      - resources 말고 다른 위치에 banner File을 위치시키는 방법  
          - application.properties
            ex) spring.banner.location = classpath:banner.txt 이런식으로..  
      - txt 말고 jpg/png/gif 로 banner를 구현할 수 있다.  
      - banner을 off 하고 싶다. 아래 코드처럼 작성하면 됨.  
      ```  
      @SpringBootApplication
      public class Application {
      public static void main(String[] args) {
        SpringApplication app = new SpringApplication(Application.class);
        app.setBannerMode(Banner.Mode.OFF); //add
        app.run(args);
        }
      }
      ```  
      - 코드로 Banner을 커스터마이징 할 수도 있다.
      - 만약 banner 모드 off하면 당연히 배너가 뜨지 않기 때문에 주석처리해야 함.
      - resources > banner.txt가 있으면 코드로 작성된 banner 메서드에 덮어씌여져 우선시되어 출력된다.
      ```  
      @SpringBootApplication
      public class Application {
        public static void main(String[] args) {
          SpringApplication app = new SpringApplication(Application.class);
          app.setBanner(new Banner() {
              @Override
              public void printBanner(Environment environment, Class<?> sourceClass, PrintStream out) {
                  out.println("=================");
                  out.println("hyeokki spring!!!");
                  out.println("=================");

              }
          });
          //app.setBannerMode(Banner.Mode.OFF);
          app.run(args);
        }
      }

      ```  
      - SpringApplicationBulder를 사용해 run을 하는 방법도 있음. 이는 커스터마이징 불가능.
      ```  
      @SpringBootApplication
      public class Application {
          public static void main(String[] args) {
              new SpringApplicationBuilder().sources(Application.class).run(args);
          }
      }
      ```  
    + Events  
        - ApplicationContext가 만들어지고 난 후 동작하는 Event와 그렇지 않은 Event가 있다.  
        - 전자의 Event경우에는 @Bean으로 등록된 Listener을 동작시키지만 후자는 그렇지 않다. 아래를 보자.  
        - Listener  
            1. ApplicationStartingEvent 구현한 Listener 클래스  
                이 Event는 Application이 동작하고 처음시점에 발생되는 Event이므로 @Bean으로 등록한다고 하더라도 동작하지 않는다.  
        ```  
        //<예제코드>
        @Component
        public class SampleListener implements ApplicationListener<ApplicationStartingEvent> {

            @Override
            public void onApplicationEvent(ApplicationStartingEvent applicationStartingEvent) {
                System.out.println("====================");
                System.out.println("ApplicationEvent!!");
                System.out.println("====================");
            }
        }
        ```  
        - 이 리스너를 동작시키려면 직접 리스너를 생성해서 동작시켜야 한다.  
        ```  
        @SpringBootApplication
        public class Application {
            public static void main(String[] args) {
                SpringApplication app = new SpringApplication(Application.class);
                app.addListeners(new SampleListener());
                app.run(args);
            }
        }
        ```  
        - ApplicationStartingEvent 말고 ApplicationStartedEvent 를 @Bean으로 등록했을 떄에는 직접 등록하지 않고 실행해도 잘 동작한다.  
          맨 마지막에 뜸.  
    + Application의 WebApplicationType을 설정
        - 3가지 타입이 있음 : Servlet, Reactive, none
        - Servlet MVC가 있으면 WebFlux가 있어도 1순위로 Servlet이 동작되고 Reactive가 2순위가 된다.  
        - 아무것도 없으면 none이 동작한다.  
        - 이는 자동으로 그렇게 동작된다는 거고, 원하는 WebApplicationType이 있다면 직접 코드로 작성해주면 된다.  
        ```  
        @SpringBootApplication
        public class Application {
            public static void main(String[] args) {
                SpringApplication app = new SpringApplication(Application.class);
                app.setWebApplicationType(WebApplicationType.REACTIVE);
                app.run(args);
            }
        }
        ```  
    + Application Arguments 사용하기
        - <Run configuration에서>  
        - Vm Option : -Dfoo  
        - Programing arguments : --bar  
        - 이렇게 해서 동작해보면 --bar만 true가 뜬다.  
        - 둘 다 console로 들어오지만, -D옵션은 VM Option에서 쓰는 것으로 치고 --로 쓰는 것만 arguments로 들어온다.  
        - 그럼 console에서는 어떨까?  
        - console에도 역시 마찬가지다. VM OPtion은 Application arguments가 아님. 
        
    + 애플리케이션 실행한 뒤 뭔가 실행하고 싶을 때
    + ApplicationRunner (추천) 또는 CommandLineRunner
        - 둘 다 JVM Option을 받지 못하고 -- 찍힌 bar만 출력됨.
    + 순서 지정 가능 @Order
        - Runner가 여러개 일 경우 순서를 주는 것임. 우선순위는 숫자가 작은게 큰것임.
    
        ``` 
        //<ApplicationRunner>
        //springboot Application이 뜬 이후에 추가적으로 실행하고 싶을때 쓰는 거라는데 log기록을 보면 Applciation 다음에 실행되서 뜸.
        @Component
        public class SampleListener  implements ApplicationRunner {

            @Override
            public void run(ApplicationArguments args) throws Exception {
                System.out.println("foo:" + args.containsOption("foo"));
                System.out.println("bar:" + args.containsOption("bar"));
            }
        }
        ``` 

***

# 외부설정  
* 주요사항  
  - application.properties의 key : value 형태  
  - application.properties 의 위치(4곳)에 따라 properties의 우선순위  
  - properties의 우선순위  
  - main > resources > application.properties **VS** test > resources > application.properties @Override  
  - property가 많은 경우 test > resources > test.properties 에서 작성하기 => @TestPropertySource(locations="classpath:/경로명")  
* 애플리케이션 설정 값들을 애플리케이션의 안밖으로 설정할 수 있음.  
* application.properties  
    + 가장 중요한 설정 파일  
    + 스프링부트가 애플리케이션을 구동할 때 자동으로 로딩하는 file 이름. convention. **이름을 똑바로 적어야 application.properties파일을 읽어들일 수 있음**  
    + key : value 형태로 저장. 애플리케이션에서 참조함.  
    + 프로퍼티 우선순위  
        1.유저 홈 디렉토리에 있는 spring-boot-dev-tools.properties  
        2.테스트에 있는 @TestPropertySource  
          - Test Properties 은 Environment 객체를 통해서 확인할 수 있음.  
          - Environment 는 springframework 의 import을 추가해야 함.  
          - environment.getPropert("hyeokki.name") 으로 properties 값을 가져올 수 있음.  
          - assertThat(environment.getProperty("hyeokki.name")).isEqualTo("hyeokki"); -> starter-test dependency 를 추가하면 사용할 수 있음. 
          - @TestPropertySource(properties = "hyeokki.name=yun2") 이 방법도 있음.  
          - @SpriongBootTest(properties = "hyeokki.name") 보다 @TestPropertySource(properties = "hyeokki.name=yun2")가 우선순위가 더 높다.  
        3.@SpringBootTest 애노테이션의 properties 애트리뷰트  
          - 3순위임  
          - @SpringBootTest(properties = "hyeokki.name=yun2") 로 참조.  
        4.커맨드 라인 아규먼트  
            - Packaging 하고 실행할 떄 .jar 뒤에 -- hyeokki.name=yun 을 추가 입력해주면 application.properties의 파일을 @Override해서 value값이 재설정됨.  
        5.SPRING_APPLICATION_JSON (환경 변수 또는 시스템 프로퍼티) 에 들어있는 프로퍼티  
        6.ServletConfig 파라미터  
        7.ServletContext 파라미터  
        8.java:comp/env JNDI 애트리뷰트  
        9.System.getProperties() 자바 시스템 프로퍼티  
        10.OS 환경 변수  
        11.RandomValuePropertySource  
        12.JAR 밖에 있는 특정 프로파일용 application properties  
        13.JAR 안에 있는 특정 프로파일용 application properties  
        14.JAR 밖에 있는 application properties  
        15.JAR 안에 있는 application properties  
        16.@PropertySource  
        17.기본 프로퍼티 (SpringApplication.setDefaultProperties)  
    
    * main > application.properties vs test > application.properties  
        - 제일 먼저 main의 src와 resources 폴더를 클래스패스에 올리고 다음에는 test를 올리는데 이때 test의 application.properties에 1개라도 같은 값이 있으면 오버라이딩이 된다.  
        - 따라서 값을 참조할 때 오버라이딩된 application.properties 을 참조하게 되고 main의 property를 추가하면 해당 애플리케이션은 빌드가 되지만 test에는 빌드가 안됀다.  
        - 이유는 당연하지만 추가된 property 속성이 오버라이딩 된 application.properties 파일에는 없기 때문에 참조할 수 가 없는 것.  
        
    * properties 가 많을 때 file로 관리하기  
        - test > resources > test.properties  
        - 2순위이기 떄문에 application.properties 에서 동일한 key값이 존재하면 덮어씌어서 test.propoerties key값을 쓰게 된다.  
    * application.properties 위치 4곳
        1. application(root) > config 디렉토리 위치  
        2. application(root) > 위치  
        3. classpath:/config/  
        4. classpath:/
        
## 외부설정2
---
  
* Configuration 클래스 생성
    + 데이터(객체)를 Binding 받을 수 있게 getter/setter 생성.  
      - 여러 properties 들을 묶어서 읽어올 수 있음.  
    + @ConfigurationProperties("hyeokki") 처리, "타입-세이프 프로퍼티"  
      - hyeokki 라는 key로 접근해서 getter 호출.  
      - configuration 클래스임을 뜻하는 애노테이션 처리.  
      - @Component를 통해서 "@Bean으로 등록해달라"는 의미의 애노테이션 처리.  
+ Application 실행 클래스에 달아둘 애노테이션  
    + @EnableConfigurationProperties(HyeokkiProperties.class)  
      - @ConfigurationProperties 클래스들을 읽어서 Bean으로 등록하고 애노테이션 처리를 하기 때문에 반드시 필요한 애노테이션.  
      - 그렇지만, @SpringBootApplication 애노테이션이 존재하면 위 기능이 포함되므로 안적어주어도 된다.  
+ Runner 클래스  
    + Configuration 객체 참조변수 선언  
      - @Autowired 애노테이션을 통해 @Bean으로 등록  
      - HyeokkiConfiguration hyeokkiConfiguration;  
    + System.out.println(hyeokkiConfiguration.getName());  
      - application.properties 에서 정의한 key값과 맵핑되어서 binding된 값을 가져옴.  
  
* 융통성 있는 바인딩
    + context-path (케밥)  
      - application.properties > hyeokki.full-name=hyeokki => '-' 허용  
    + context_path (언드스코어)  
      - application.properties > hyeokki.full_name=hyeokki => '_' 허용
    + contextPath (캐멀)  
    + CONTEXTPATH  
* 프로퍼티 타입 컨버전
    + application.properties 파일에 작성된 내용은 문자열이기 때문에 property값을 int 속성으로 주입받고자 하는 경우  
      자동적으로 Integer 타입으로 컨버전하는 기능을 @ConfigurationProperties에서 지원해줍니다.  
* @DurationUnit  
    + 시간정보기능  
    + 필요하다면 강의 내용 중 외부설정2 > 6:10 부터  
* 프로퍼티 값 검증  
    + @Validated  
    + JSR-303 (@NotNull, ...)  
* 메타 정보 생성
    + "spring-boot-configuration-prosessor" dependency Injection.  
    + 프로젝트를 빌드할 때 META정보를 생성해주는 plugin.  
+ @Value  
SpEL 을 사용할 수 있지만, 위에 있는 @ConfigurationProperties가 지원하는 기능은 전부 사용하지 못함.  
  
  
***
  
## 프로파일  
  
* SpringFramework에서 제공해주는 기능.  
* 특정한 profile에서만 특정 bean 등록하고 싶을 때  
* 애플리케이션 동작을 특정 profile에서 다르게 동작하고 싶을 때 사용.  
  
* @Bean설정  
    + Profile("") 값에 해당이 되면 @Configuration 을 통해 @Bean이 등록이 되고, 해당이 안되면 클래스를 읽지 않음.  
    + 사용법  
      - application.properties 에서 key 추가  
        ex)spring.profiles.actice=prod  
      - command line 의 arguments 값으로 profile.active 값을 주면 우선순위에 따라 application.properties 을 오버라이드 해서 값이 변경되어 값이 출력된다.  
* profile과 관련된 properties 는 기본 application.properties 보다 우선순위가 더 높다.  
    + application-prod.properties, application-test.properties >(우선순위)> application.properties  
+ spring.profiles.include 속성 :  추가적인 profile을 활성화.  
  
  
***  
  
## logging  
  
* 주제
  - 스프링부트 기본 로거 설정  
  - 커스터마이징  
---

### 스프링부트 기본 로거 설정  
* 로깅 퍼사드 VS 로거 : commons Logging은 여러 개의 로거들을 골라서 사용할 수 있다는 장점이 있으나 클래스패스와 관려 이슈가 있었음.  
                    SLF4j는 클래스 로더에서 발생하는 이슈들은 해결되 수 있으나 exclusion의 의존성 설정이 조금 복잡함 => commons logging 기능 + 의존성에 근거해서 최종 선택을 함. 
                    spring5 에서 exclusion 하지 않아도 안전하게 쓸 수 있도록 자체 내에서 스프링-JCL 모듈을 만듬.  
                    이는 스프링-JCL 코드를 컴파일 시점에 SLF4j 혹은 Log4J2로 변경할 수 있는 기능을 가진 모듈임.  
                    정리하자면 commons logging을 사용해도 SLF4j로 가게 되고 SLF4j는 Logback으로 기게 됨.  
                    스프링 부트에서 만든 애플리케이션에서 무슨 로거를 사용해서 찍었는지 물어본다면 logback을 통해 로그를 찍힌 것이 정답임.  
* Commons Logging, SLF4j  
  - JUL, Log4J2, Logback  
  
* 스프링 5에 로거 관련 변경 사항  
  https://docs.spring.io/spring/docs/5.0.0.RC3/spring-framework-reference/overview.html#overview-logging  
  
* Spring-JCL  
  :Commons Logging -> SLF4j or Log4j2  
   pom.xml에 exclusion 안해도 됨.  
   
* 스프링 부트 로깅(Run/debug configuration **OR** application.properties 설정파일)  
  + 기본 포맷  
  + --debug (일부 핵심 라이브러리만 디버깅 모드로)  
  + --trace (전부 다 디버깅 모드로)  
  + 컬러 출력: spring.output.ansi.enabled  
  + 파일 출력: logging.file 또는 logging.path  
  + 로그 레벨 조정: logging.level.패키지경로 = 로그 레벨  
    - 다른 패키지 로그 레벨도 출력해볼 수 있다.(패키지 명만 바꾸면 됨)  

### 커스터마이징  

* [커스텀 로그 설정 파일 사용하기](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html)  
  
#### 커스터마이징 설정 파일들  
* Logback: logback-spring.xml  
    + 추가 기능 제공  
    + Logback extension  
      - 프로파일 <springProfile name=”프로파일”>  
      - Environment 프로퍼티 <springProperty>   
  
* Log4J2: log4j2-spring.xml  
    + 위와 동일한 추가기능 제공  
    + 기본 설정은 logback과 동일하게 되어 있기 때문에 로그형태는 똑같이 출력됨.  
* JUL (비추): logging.properties  
  
* 로거를 Log4j2로 변경하기([Reference-Link](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html#howto-configure-log4j-for-logging))

* 정리
  + 스프링 5에서 spring-jcl이 추가되어서 slf4j를 추가 설정하지 않아도 사용할 수 있게 됨.  
  + slf4j의 기본로거인 logback이 최종적으로 로그를 출력함.  
  + 기본 로거를 바꾸기 위해서는 web의존성에 logging 관련 의존성이 포함되어 있기 때문에 exclusion을 사용해서 logging 의존성을 제외해주고 변경하려는 의존성을 새로 추가해줘야 한다.  
  + but, log4j2 로그의 기본설정이 logback과 동일하기 때문에 출력형태는 같다.  
  
***  
## 테스트  
[공부 전]  
Q. 애플리케이션 테스트가 중요하다고 하는데 스프링부트에서 제공해주는 편리한 테스트 기능인가?  
Q. 역시 이번에도 의존성을 추가하거나 애노테이션을 사용하게 되겠지? 아니면 편리하게 사용할 수 있도로 관려 설정파일을 다루거나.  
자, 이제 공부 시작!  

[공부 후-처음]  
A. 하나도 모르겠다. 아니, 사실 스프링 부트에서 이런 테스트를 왜 하는가. 어떤상황일때 테스트를 하는가를 모르겠다.  
A. 백기선님은 이런 테스트 애노테이션이 있어서 편리하다는 듯 말씀하시는데, 나는 공감을 못하겠다.  
A. 공감을 못하는게 당연하다. 현재 나는 애플리케이션을 개발해본 적 없고 테스트가 왜 중요한지 모르고, 테스트의 나쁜 코드를 짜본적도, 이로인해 귀찮음을 느껴본 적이 없다.  
A. 테스트는 중요하다고 말하니 알겠다. 현재로써 내가 해야 할 일을 정하자면  
A. 이해될 때까지 영상반복 + 구글검색 을 통해서 테스트 기능을 외운다. 그리고 개발 시 사용하며, 왜 사용해야 하는 지 알아본다.  
A. 다시 공부 시작!  

* 테스트 시작  
  + 의존성 부터 확인.  
  + 테스트 클래스 작성.(테스트 환경이 Mock일 때, MockMVC 를 활용한 테스트 작성.) 
  + @Runwith(SpringRunner.class), @SpringbootTest => 가장 기본적인 테스트 코드 형태.  
  + @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)-[참고](https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-testing)  
  + MockMVC([참고](https://shinsunyoung.tistory.com/52))
    - 실제 객체와 비슷하지만 테스트에 필요한 기능만 가지는 가짜 객체를 만들어서  
      애플리케이션 서버에 배포하지 않고도 스프링 MVC 동작을 재현할 수 있는 클래스를 의미  
  ```  
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
  @AutoConfigureMockMvc
  public class SampleControllerTest {

      @Autowired
      MockMvc mockMvc;

      @Test
      public void hello() throws Exception{
          mockMvc.perform(MockMvcRequestBuilders.get("/hello"))
                  .andExpect(status().isOk())
                  .andExpect(content().string("hello hyeokki"))
                  .andDo(print());

      }

  }
  ```  
* 테스트 작성(랜덤 포트를 사용 - @Service 단위로 테스트하기.)  
  + 내장 톰켓에 요청 보내서 테스트용 서블릿컨테이너가 뜬다(?).  
  + 코드  
  ```  
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {

      @Autowired
      TestRestTemplate testRestTemplate;

      @Test
      public void hello() throws Exception{
          String result = testRestTemplate.getForObject("/hello", String.class);//URL, 내가 원하는 바디 타입.
          assertThat(result).isEqualTo("hello hyeokki");
      }
  }
  ```  
* 테스트 작성(@Controller 단위로 테스트하기-@Service보다 범위가 작음.)  
  + SampleController 에서 서비스하는 @Bean을 Mocking해서 @MockBean으로 교체, 테스트하기 편해짐.  
  ```  
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {

      @Autowired
      TestRestTemplate testRestTemplate;

      @MockBean
      SampleService mockSampleService; // applicationContext 안에 있는 실제 bean을 MockBean으로 교체

      @Test
      public void hello() throws Exception{
          when(mockSampleService.getName()).thenReturn("yun");
          String result = testRestTemplate.getForObject("/hello", String.class);//URL, 내가 원하는 바디 타입.
          assertThat(result).isEqualTo("hello yun");
      }

  }
  ```  
* WebTestClien
  ```
  @RunWith(SpringRunner.class)
  @SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
  @AutoConfigureMockMvc
  public class SampleControllerTest {

      @Autowired
      WebTestClient webTestClient;

      @MockBean
      SampleService mockSampleService; // applicationContext 안에 있는 실제 bean을 MockBean으로 교체

      @Test
      public void hello() throws Exception{
          when(mockSampleService.getName()).thenReturn("yun");

          webTestClient.get().uri("/hello").exchange().expectStatus().isOk()
                      .expectStatus().isOk()
                      .expectBody(String.class).isEqualTo("hello yun");
      }

  }
  ```  
  
* 테스트 유틸  
  + OutputCapture  
    : 로그를 비롯해서 콘솔에 찍히는 모든 것을 다 Capture함.  
  ```  
  @RestController
  public class SampleController {

      Logger logger = LoggerFactory.getLogger(SampleController.class);

      @Autowired
      private SampleService sampleService;

      @GetMapping("/hello")
      public String hello(){
          logger.info("holoman");
          System.out.println("skip");
          return "hello "+sampleService.getName();
      }
  }

  ```  
  + TestPropertyValues  
  + TestRestTemplate  
  + ConfigFileApplicationContextInitializer  
***  
  
  
## 스프링 웹 MVC  
  
### 소개  
* @WebMvcTest(UserController.class)  
  WebMvcTest를 만들 때 주로 사용하는 Mockmvc라는 객체를 주입.  
  이 객체는 WebMvcTest라는 애노테이션을 사용하면 자동으로 @bean으로 만들어주기 때문에 우리가 그냥 @Bean에 있는 걸 꺼내다가 바로 사용할 수 있다.  
  ```  
    @RunWith(SpringRunner.class)
    @WebMvcTest(UserController.class)//슬라이싱 테스트(웹계층)
    public class UserControllerTest {
        @Autowired

    }
  ```  

### HttpMessageConverters
* [Reference](https://docs.spring.io/spring/docs/5.0.7.RELEASE/spring-framework-reference/web.html#mvc-config-message-converters)  
* 스프링 프레임워크가 제공하는 인터페이스이고 스프링MVC의 일부분.  
* HTTP 요청 본문을 객체로 변경하거나, 객체를 HTTP 응답 본문으로 변경할 때 사용.
  스프링MVC에서 RequestBody와 ResponseBody 애노테이션과 같이 사용됨.  
  어떤 요청을 받았는지, 어떤 응답을 보내야하는지에 따라 사용하는 HttpMessageConverters가 달라짐.  
  가령, 요청헤더타입:JSON, 본문:JSON : JSON메세지컨버터가 사용되서 JSON메세지르 user라는 객체로 컨버터 해준다.  
  ```  
    @PostMapping("/user")
    public @ResponseBody User create(@RequestBody User user){ // 클래스에서 알아서 conversion 해준다.
        return null;
    }
    //@RestController 애노테이션이 붙어 있으면 @ResponseBody는 생략이 가능하다. 
  ```  
* {“username”:”keesun”, “password”:”123”} <-> User
* @ReuqestBody
* @ResponseBody





  
  
