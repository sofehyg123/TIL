# Spring_boot

* [출처:백기선님의 inflearn 강좌](https://www.inflearn.com/course/스프링부트/dashboard)  
---
* 꺠닫기
  + 환경설정이 오타가 나거나 잘못된 데이터를 맵핑하는 코드를 넣으면 맵핑값을 찾을 수 없게 되어서 오류가 뜬다.  
# 목차  
  
0. [자동설정 이해](#자동설정-이해)
1. [자동설정 만들기](#자동설정-만들기)  
2. [내장 웹서버의 이해](#내장-웹서버의-이해)  
3. [내장 웹서버의 응용1](#내장-웹서버의-응용1)  
4. [내장 웹서버의 응용2](#내장-웹서버의-응용2)  
5. [독립적으로 실행 가능한 JAR 파일](#독립적으로-실행-가능한-JAR-파일)  
6. [spring application](#spring-application)  
7. [외부설정](#외부설정)  
8. [프로파일](#프로파일)  
  
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
        - 근데 전부 다 구동되는 것은 아니고 조건에 맞게 구동이 된다는 것.
  
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
  
* 로깅 퍼사드 VS 로거 : commons Logging은 여러 개의 로거들을 골라서 사용할 수 있다는 장점이 있으나 클래스패스와 관려 이슈가 있었음.  
                    SLF4j 는 클래스 로더에서 발생하는 이슈들은 해결되 수 있으나 exclusion의 의존성 설정이 조금 복잡함.  
                    spring5 에서 exclusion 하지 않아도 안전하 쓸 수 있도록 자체 내에서 스프링jcl 모듈을 만듬.  
                    jcl 코드르 컴파이 시점에 SLF4j 혹은 Log4J2로 f
Commons Logging, SLF4j  
JUL, Log4J2, Logback  
      

  
  
